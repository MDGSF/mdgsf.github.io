---
layout: post
title:  "[Go By Example] Channels"
date:   2018-02-28
comments: true
categories: GoByExample
tags: GO
description: ""
published: true
---

```go
package main

import "fmt"

func main() {
	messages := make(chan string)

	go func() {
		messages <- "ping"
	}()

	msg := <-messages
	fmt.Println(msg)
}
```

```
$ go run test.go
ping
```

[https://gobyexample.com/channels](https://gobyexample.com/channels)

