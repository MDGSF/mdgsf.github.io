---
layout: post
title:  "[Go By Example] Closures"
date:   2018-02-08
comments: true
categories: GoByExample
tags: GO
description: ""
published: true
---

```go
package main

import "fmt"

func intSeq() func() int {
	i := 0
	return func() int {
		i++
		return i
	}
}

func main() {
	nextInt := intSeq()

	fmt.Println(nextInt())
	fmt.Println(nextInt())
	fmt.Println(nextInt())

	newInts := intSeq()
	fmt.Println(newInts())
}
```

```
$ go run test.go
1
2
3
1
```

[https://gobyexample.com/closures](https://gobyexample.com/closures)
