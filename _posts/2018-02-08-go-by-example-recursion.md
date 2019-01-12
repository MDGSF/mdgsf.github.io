---
layout: post
title:  "[Go By Example] Recursion"
date:   2018-02-08
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
)

func fact(n int) int {
	if n == 0 {
		return 1
	}
	return n * fact(n-1)
}

func main() {
	fmt.Println(fact(7))
}
```

```
$ go run test.go
5040
```

[https://gobyexample.com/recursion](https://gobyexample.com/recursion)
