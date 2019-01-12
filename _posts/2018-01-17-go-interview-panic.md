---
layout: post
title:  "[GO] panic"
date:   2018-01-17
comments: true
categories: GO
tags: GO
description: ""
published: true
---

```go
package main

import "fmt"

func main() {
	/*
	   先defer的后执行
	   recover后输出panic中的信息
	*/

	defer func() {
		if err := recover(); err != nil {

			fmt.Print(err)
		} else {
			fmt.Print("no")
		}

	}()
	defer func() {
		panic("1111111111111")
	}()
	panic("22222222222")

}
```

```
$ go run test.go
1111111111111
```