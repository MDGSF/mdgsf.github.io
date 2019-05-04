---
layout: post
title:  "[GO] goroutine"
date:   2018-01-17
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
    "runtime"
    "sync"
)

func init() {
    fmt.Println("Current Go Version:", runtime.Version())
}
func main() {
    runtime.GOMAXPROCS(1)
    count := 10
    wg := sync.WaitGroup{}
    wg.Add(count * 2)
    for i := 0; i < count; i++ {
        go func() {
            fmt.Printf("[%d]", i)
            wg.Done()
        }()
    }
    for i := 0; i < count; i++ {
        go func(i int) {
            fmt.Printf("-%d-", i)
            wg.Done()
        }(i)
    }
    wg.Wait()
}
```

```
$ go run test.go
Current Go Version: go1.9.2
-9-[10][10][10][10][10][10][10][10][10][10]-0--1--2--3--4--5--6--7--8-
```

这个暂时不知道为什么，应该是和协程调度有关，需要去看下go协程调度的源码。
