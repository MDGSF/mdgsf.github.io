---
layout: post
title:  "[GO] goroutines"
date:   2017-09-19
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

func say(s string) {
	for i := 0; i < 5; i++ {
		time.Sleep(100 * time.Millisecond)
		fmt.Println(s)
	}
}

func main() {
	go say("world")
	say("hello")
}
```

