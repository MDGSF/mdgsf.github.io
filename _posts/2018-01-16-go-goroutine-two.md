---
layout: post
title:  "[GO] 两协程交替输出数字"
date:   2018-01-16
comments: true
categories: GO
tags: GO
description: ""
published: true
---


```go
package main

import (
	"fmt"
	"time"
)

func main() {

	for i := 1; i <= 10; i++ {
		go func(i int) {
			fmt.Println(2*i - 1)
		}(i)
	}

	for i := 1; i <= 10; i++ {
		go func(i int) {
			fmt.Println(2 * i)
		}(i)
	}

	time.Sleep(3 * time.Second)
}
```

### 修正

```go
package main

import (
	"fmt"
)

func main() {

	a := make(chan int)
	b := make(chan int)
	exit := make(chan int)

	go func() {
		for i := 1; i <= 20; i++ {
			fmt.Println(<-a)
			i++
			b <- i
		}
	}()

	go func() {
		for i := 0; i < 20; i++ {
			i++
			a <- i
			fmt.Println(<-b)
		}
		close(exit)
	}()

	<-exit
}
```
