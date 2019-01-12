---
layout: post
title:  "[Go By Example] Variables"
date:   2018-01-17
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
	var a string = "initial"
	fmt.Println(a)

	var b, c int = 1, 2
	fmt.Println(b, c)

	var d = true
	fmt.Println(d)

	var e int
	fmt.Println(e)

	f := "short"
	fmt.Println(f)
}
```

```
$ go run test.go
initial
1 2
true
0
short
```