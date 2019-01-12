---
layout: post
title:  "[GO] 接收者为指针的方法"
date:   2017-09-19
comments: true
categories: GO
tags: GO
description: ""
published: true
---

## 1

 方法可以与命名类型或命名类型的指针关联。

刚刚看到的两个 Abs 方法。一个是在 *Vertex 指针类型上，而另一个在 MyFloat 值类型上。 有两个原因需要使用指针接收者。首先避免在每个方法调用中拷贝值（如果值类型是大的结构体的话会更有效率）。其次，方法可以修改接收者指向的值。

尝试修改 Abs 的定义，同时 Scale 方法使用 Vertex 代替 *Vertex 作为接收者。

当 v 是 Vertex 的时候 Scale 方法没有任何作用。Scale 修改 v。当 v 是一个值（非指针），方法看到的是 Vertex 的副本，并且无法修改原始值。

Abs 的工作方式是一样的。只不过，仅仅读取 v。所以读取的是原始值（通过指针）还是那个值的副本并没有关系。


```go
package main

import (
	"fmt"
	"math"
)

type Vertex struct {
	X, Y float64
}

func (v *Vertex) Scale(f float64) {
	v.X = v.X * f
	v.Y = v.Y * f
}

func (v *Vertex) Abs() float64 {
	return math.Sqrt(v.X*v.X + v.Y*v.Y)
}

func main() {
	v := &Vertex{3, 4}
	fmt.Printf("Before scaling: %+v, Abs: %v\n", v, v.Abs())
	v.Scale(5)
	fmt.Printf("After scaling: %+v, Abs: %v\n", v, v.Abs())
}
```

输出:

```
Before scaling: &{X:3 Y:4}, Abs: 5
After scaling: &{X:15 Y:20}, Abs: 25
```

如果去掉指针，则输出:

```
Before scaling: &{X:3 Y:4}, Abs: 5
After scaling: &{X:3 Y:4}, Abs: 5
```

## 2

但是下面这种情况，没有指针的函数，居然修改也有效，就是Swap这个函数

```go
package main

import (
	"fmt"
)

type Point struct {
	a, b int
}

type intPairs []Point

// IntPairs compare on a only.
func (d intPairs) Len() int           { return len(d) }
func (d intPairs) Less(i, j int) bool { return d[i].a < d[j].a }
func (d intPairs) Swap(i, j int)      { d[i], d[j] = d[j], d[i] }

func main() {
	var d intPairs

	p1 := Point{1, 2}
	p2 := Point{3, 4}
	p3 := Point{5, 6}
	d = append(d, p1)
	d = append(d, p2)
	d = append(d, p3)

	fmt.Println(d)

	d.Swap(1, 2)
	fmt.Println(d)
}
```

输出:

```
[{1 2} {3 4} {5 6}]
[{1 2} {5 6} {3 4}]
```

