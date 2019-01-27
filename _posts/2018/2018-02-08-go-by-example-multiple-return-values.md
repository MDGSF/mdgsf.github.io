---
layout: post
title:  "[Go By Example] Multiple Return Values"
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

func vals() (int, int) {
	return 3, 7
}

func main() {
	a, b := vals()
	fmt.Println(a)
	fmt.Println(b)

	_, c := vals()
	fmt.Println(c)
}
```

```
$ go run test.go
3
7
7
```

[https://gobyexample.com/multiple-return-values](https://gobyexample.com/multiple-return-values)
