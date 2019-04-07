---
layout: post
title:  "[GO] slice自动分配内存"
date:   2018-01-10
comments: true
categories: GO
tags: [GO]
description: ""
published: true
---


```go
package main

import "fmt"

func main() {

	s := make([]int, 0, 3)
	fmt.Println(len(s), cap(s), s) //0 3 []

	s = append(s, 9)
	fmt.Println(len(s), cap(s), &s[0], s) //1 3 0xc0420560a0 [9]

	s = append(s, 8)
	fmt.Println(len(s), cap(s), &s[0], s) //2 3 0xc0420560a0 [9 8]

	s = append(s, 7)
	fmt.Println(len(s), cap(s), &s[0], s) //3 3 0xc0420560a0 [9 8 7]

	//这里容量不足了，会重新分配内存，所以&s[0]改变了。
	s = append(s, 6)
	fmt.Println(len(s), cap(s), &s[0], s) //4 6 0xc042074090 [9 8 7 6]

	s = append(s, 5)
	fmt.Println(len(s), cap(s), &s[0], s) //5 6 0xc042074090 [9 8 7 6 5]

	s = append(s, 4)
	fmt.Println(len(s), cap(s), &s[0], s) //6 6 0xc042074090 [9 8 7 6 5]

	//这里容量又不足了，会重新分配内存，所以&s[0]改变了。
	s = append(s, 3)
	fmt.Println(len(s), cap(s), &s[0], s) //7 12 0xc04204a0c0 [9 8 7 6 5 4 3]
}
```

[Go语言圣经 《The Go Programming Language》 中文版本](https://books.studygolang.com/gopl-zh/ch4/ch4-02.html)

