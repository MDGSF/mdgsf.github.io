---
layout: post
title:  "[GO] closures"
date:   2017-09-19
comments: true
categories: GO
tags: [GO]
description: ""
published: true
---



 Go 函数可以是一个闭包。闭包是一个函数值，它引用了函数体之外的变量。 这个函数可以对这个引用的变量进行访问和赋值；换句话说这个函数被“绑定”在这个变量上。

例如，函数 adder 返回一个闭包。每个返回的闭包都被绑定到其各自的 sum 变量上。 

```go
package main

import "fmt"

func adder() func(int) int {
	sum := 0
	return func(x int) int {
		sum += x
		return sum
	}
}

func main() {
	pos, neg := adder(), adder()
	for i := 0; i < 10; i++ {
		fmt.Println(
			pos(i),
			neg(-2*i),
		)
	}
}
```

## fibonacci

```go
package main

import "fmt"

// fibonacci 函数会返回一个返回 int 的函数。
func fibonacci() func() int {
	a := 1
	b := 1
	return func() int {
		s := a
		a, b = b, a + b
		return s
	}
}

func main() {
	f := fibonacci()
	for i := 0; i < 10; i++ {
		fmt.Println(f())
	}
}
```
