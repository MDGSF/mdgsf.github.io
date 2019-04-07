---
layout: post
title:  "[GO] channels"
date:   2017-09-19
comments: true
categories: GO
tags: [GO]
description: ""
published: true
---


（“箭头”就是数据流的方向。）

和 map 与 slice 一样，channel 使用前必须创建：

```go
ch := make(chan int)
```

默认情况下，在另一端准备好之前，发送和接收都会阻塞。这使得 goroutine 可以在没有明确的锁或竞态变量的情况下进行同步。 


## Examples

```go
package main

import "fmt"

func sum(a []int, c chan int) {
	sum := 0
	for _, v := range a {
		sum += v
	}
	c <- sum
}

func main() {
	a := []int{7, 2, 8, -9, 4, 0}

	c := make(chan int)
	go sum(a[:len(a)/2], c)
	go sum(a[len(a)/2:], c)
	x, y := <-c, <-c
	fmt.Println(x, y, x+y)
}
```
