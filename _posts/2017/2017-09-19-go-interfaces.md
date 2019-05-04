---
layout: post
title:  "[GO] Interface"
date:   2017-09-19
comments: true
categories: GO
tags: [GO]
description: ""
published: true
---

## 1

接口类型是由一组方法定义的集合。

接口类型的值可以存放实现这些方法的任何值。

注意： 示例代码的 22 行存在一个错误。 由于 Abs 只定义在 *Vertex（指针类型）上， 所以 Vertex（值类型）不满足 Abser。

```go
package main

import (
    "fmt"
    "math"
)

type Abser interface {
    Abs() float64
}

func main() {
    var a Abser
    f := MyFloat(-math.Sqrt2)
    v := Vertex{3, 4}

    a = f  // a MyFloat 实现了 Abser
    a = &v // a *Vertex 实现了 Abser

    // 下面一行，v 是一个 Vertex（而不是 *Vertex）
    // 所以没有实现 Abser。
    a = v

    fmt.Println(a.Abs())
}

type MyFloat float64

func (f MyFloat) Abs() float64 {
    if f < 0 {
        return float64(-f)
    }
    return float64(f)
}

type Vertex struct {
    X, Y float64
}

func (v *Vertex) Abs() float64 {
    return math.Sqrt(v.X*v.X + v.Y*v.Y)
}
```

## 2

将对象赋值给接口时，会发生拷贝，而接口内部存储的是指向这个复制品的指针，既无法修改复制品的状态，也无法获取指针。

```go
package main

import (
    "fmt"
)

type Connector interface {
    Connect()
}

type PhoneConnecter struct {
    name string
}

func (pc PhoneConnecter) Name() string{
    return pc.name
}

func (pc PhoneConnecter) Connect() {
    fmt.Println("connected:", pc.name)
}

func main() {
    pc := PhoneConnecter{"PhoneConnecter"}
    var a Connector
    a = Connector(pc)
    a.Connect()

    pc.name = "pc"
    a.Connect()
}
```

输出：

```
$ go run test.go
connected: PhoneConnecter
connected: PhoneConnecter
```

## 3

只有当接口存储的类型和对象都为 nil 时，接口才等于 nil

```go
package main

import (
    "fmt"
)

func main() {
    var a interface{}
    fmt.Println("a == nil", a == nil)

    var p * int = nil
    a = p
    fmt.Println("a == nil", a == nil)
}
```
