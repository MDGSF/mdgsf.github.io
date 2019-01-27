---
layout: post
title:  "[Go By Example] For"
date:   2018-01-17
comments: true
categories: GoByExample
tags: [Go]
description: ""
published: true
---

```go
package main

import "fmt"

func main() {
	i := 1
	for i <= 3 {
		fmt.Println(i)
		i = i + 1
	}

	for j := 7; j <= 9; j++ {
		fmt.Println(j)
	}

	for {
		fmt.Println("loop")
		break
	}

	for n := 0; n <= 5; n++ {
		if n%2 == 0 {
			continue
		}
		fmt.Println(n)
	}
}
```

```
$ go run test.go
1
2
3
7
8
9
loop
1
3
5
```

[https://gobyexample.com/for](https://gobyexample.com/for)
