---
layout: post
title:  "[Go By Example] Functions"
date:   2018-02-08
comments: true
categories: GoByExample
tags: [Go]
description: ""
published: true
---

```go
package main

import "fmt"

func plus(a int, b int) int {
	return a + b
}

func plusplus(a, b, c int) int {
	return a + b + c
}

func main() {
	res := plus(1, 2)
	fmt.Println("1+2=", res)

	res = plusplus(1, 2, 3)
	fmt.Println("1+2=", res)
}
```

```
$ go run test.go
1+2= 3
1+2= 6
```

[https://gobyexample.com/functions](https://gobyexample.com/functions)
