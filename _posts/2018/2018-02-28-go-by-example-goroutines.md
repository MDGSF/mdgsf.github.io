---
layout: post
title:  "[Go By Example] Goroutines"
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

func f(from string) {
	for i := 0; i < 3; i++ {
		fmt.Println(from, ":", i)
	}
}

func main() {
	f("direct")

	go f("goroutine")

	go func(msg string) {
		fmt.Println(msg)
	}("going")

	fmt.Scanln()
	fmt.Println("done")
}
```

[https://gobyexample.com/goroutines](https://gobyexample.com/goroutines)

