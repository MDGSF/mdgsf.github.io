---
layout: post
title:  "[Go By Example] Channel Synchronization"
date:   2018-02-28
comments: true
categories: GoByExample
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

func worker(done chan bool) {
	fmt.Print("working...")
	time.Sleep(time.Second)
	fmt.Println("done")

	done <- true
}

func main() {
	done := make(chan bool, 1)
	go worker(done)

	<-done
}
```

```
$ go run test.go
working...done
```

[https://gobyexample.com/channel-synchronization](https://gobyexample.com/channel-synchronization)

