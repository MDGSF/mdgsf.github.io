---
layout: post
title:  "[GO] Worker线程"
date:   2018-01-16
comments: true
categories: GO
tags: [GO]
description: ""
published: true
---


```go
package main

import (
	"fmt"
	"math/rand"
	"time"
)

func main() {
	c := make(chan int)
	for i := 0; i < 5; i++ {
		worker := &Worker{id: i}
		go worker.process(c)
	}

	for {
		c <- rand.Int()
		time.Sleep(time.Millisecond * 500)
	}
}

type Worker struct {
	id int
}

func (w *Worker) process(c chan int) {
	for {
		data := <-c
		fmt.Printf("worker %d got %d\n", w.id, data)
	}
}
```

输出：

```
$ go run test.go
worker 4 got 5577006791947779410
worker 1 got 8674665223082153551
worker 2 got 6129484611666145821
worker 3 got 4037200794235010051
worker 0 got 3916589616287113937
worker 4 got 6334824724549167320
worker 1 got 605394647632969758
...
```

我们不知道哪个 `worker` 将获得数据。我们所知道的是，Go 语言确保，往一个通道发送数据时，仅有一个单独的接收器可以接收。

**注意：**通道是唯一共享的状态，通过通道，可以安全的，并发发送和接收数据。通道提供了我们需要的所有同步代码，并且也确保，在任意的特定时刻，只有一个 Go 协程，可以访问数据的特定部分。


### 使用 select

```go
package main

import (
	"fmt"
	"math/rand"
	"time"
)

func main() {
	c := make(chan int)
	for i := 0; i < 5; i++ {
		worker := &Worker{id: i}
		go worker.process(c)
	}

	for {
		select {
		case c <- rand.Int():
		case <-time.After(time.Millisecond * 100):
			fmt.Println("timed out")
		}
		time.Sleep(time.Millisecond * 50)
	}
}

type Worker struct {
	id int
}

func (w *Worker) process(c chan int) {
	for {
		data := <-c
		fmt.Printf("worker %d got %d\n", w.id, data)
		time.Sleep(time.Second * 2)
	}
}
```

`select` 工作机制：

- 第一个可用的通道被选中
- 如果多个通道可用，随机选中一个通道
- 如果没有通道可用，`default` 分之被执行
- 如果没有 `default` 分支，`select` 将阻塞

