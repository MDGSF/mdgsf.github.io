---
layout: post
title:  "[Go By Example] Structs"
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

type person struct {
	name string
	age  int
}

func main() {
	fmt.Println(person{"Bob", 20})

	fmt.Println(person{name: "Alice", age: 30})

	fmt.Println(person{name: "Fred"})

	fmt.Println(&person{name: "Ann", age: 40})

	s := person{name: "Sean", age: 50}
	fmt.Println(s.name)

	sp := &s
	fmt.Println(sp.age)

	sp.age = 51
	fmt.Println(sp.age)
}
```

```
$ go run test.go
{Bob 20}
{Alice 30}
{Fred 0}
&{Ann 40}
Sean
50
51
```

[https://gobyexample.com/structs](https://gobyexample.com/structs)
