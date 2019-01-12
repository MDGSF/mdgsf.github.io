---
layout: post
title:  "[Go By Example] Variadic Functions"
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

func sum(nums ...int) {
	fmt.Print(nums, " ")
	total := 0
	for _, num := range nums {
		total += num
	}
	fmt.Println(total)
}

func main() {
	sum(1, 2)
	sum(1, 2, 3)

	nums := []int{1, 2, 3, 4}
	sum(nums...)
}
```

```
$ go run test.go
[1 2] 3
[1 2 3] 6
[1 2 3 4] 10
```

[https://gobyexample.com/variadic-functions](https://gobyexample.com/variadic-functions)
