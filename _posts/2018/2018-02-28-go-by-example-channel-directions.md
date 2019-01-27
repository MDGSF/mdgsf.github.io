---
layout: post
title:  "[Go By Example] Channel Directions"
date:   2018-02-28
comments: true
categories: GoByExample
tags: [Go]
description: ""
published: true
---

```go
package main

import "fmt"

func ping(pings chan<- string, msg string) {
    pings <- msg
}

func pong(pings <-chan string, pongs chan<- string) {
    msg := <-pings
    pongs <- msg
}

func main() {
    pings := make(chan string, 1)
    pongs := make(chan string, 1)
    ping(pings, "passed message")
    pong(pings, pongs)
    fmt.Println(<-pongs)
}
```

```
$ go run test.go
passed message
```

[https://gobyexample.com/channel-directions](https://gobyexample.com/channel-directions)

