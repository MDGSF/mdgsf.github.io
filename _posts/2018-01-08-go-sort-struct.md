---
layout: post
title:  "[GO] 排序结构体"
date:   2018-01-08
comments: true
categories: GO
tags: [Go]
description: ""
published: true
---

### Example 1

```go
package main

import (
	"log"
	"sort"
)

type Stu struct {
	id   int
	name string
}

var class []*Stu

type ByID []*Stu

func (s ByID) Len() int {
	return len(s)
}
func (s ByID) Swap(i, j int) {
	s[i], s[j] = s[j], s[i]
}
func (s ByID) Less(i, j int) bool {
	return s[i].id < s[j].id
}

type ByName []*Stu

func (s ByName) Len() int {
	return len(s)
}
func (s ByName) Swap(i, j int) {
	s[i], s[j] = s[j], s[i]
}
func (s ByName) Less(i, j int) bool {
	return s[i].name < s[j].name
}

func NewDemoClass() {
	stu1 := &Stu{11, "huang"}
	stu2 := &Stu{2, "jian"}
	stu3 := &Stu{33, "ping"}
	stu4 := &Stu{4, "ao"}
	stu5 := &Stu{5, "lili"}

	class = append(class, stu1)
	class = append(class, stu2)
	class = append(class, stu3)
	class = append(class, stu4)
	class = append(class, stu5)
}

func showClass() {
	for _, v := range class {
		log.Println(*v)
	}
}

func main() {
	NewDemoClass()

	sort.Sort(ByID(class))
	showClass()
	log.Println()

	sort.Sort(ByName(class))
	showClass()
	log.Println()
}
```

### Example 2

```go
package main

import (
	"log"
	"sort"
)

type Stu struct {
	id   int
	name string
}

var class []Stu

type ByID []Stu

func (s ByID) Len() int {
	return len(s)
}
func (s ByID) Swap(i, j int) {
	s[i], s[j] = s[j], s[i]
}
func (s ByID) Less(i, j int) bool {
	return s[i].id < s[j].id
}

type ByName []Stu

func (s ByName) Len() int {
	return len(s)
}
func (s ByName) Swap(i, j int) {
	s[i], s[j] = s[j], s[i]
}
func (s ByName) Less(i, j int) bool {
	return s[i].name < s[j].name
}

func NewDemoClass() {
	stu1 := Stu{11, "huang"}
	stu2 := Stu{2, "jian"}
	stu3 := Stu{33, "ping"}
	stu4 := Stu{4, "ao"}
	stu5 := Stu{5, "lili"}

	class = append(class, stu1)
	class = append(class, stu2)
	class = append(class, stu3)
	class = append(class, stu4)
	class = append(class, stu5)
}

func showClass() {
	for _, v := range class {
		log.Println(v)
	}
}

func main() {
	NewDemoClass()

	sort.Sort(ByID(class))
	showClass()
	log.Println()

	sort.Sort(ByName(class))
	showClass()
	log.Println()
}
```

### Example 3

```go
package main

import (
	"fmt"
	"sort"
)

type Shop struct {
	Price float32
	Date  string
}

type ByPriceData []Shop

func (s ByPriceData) Len() int {
	return len(s)
}

func (s ByPriceData) Swap(i, j int) {
	s[i], s[j] = s[j], s[i]
}

func (s ByPriceData) Less(i, j int) bool {
	if s[i].Price > s[j].Price {
		return true
	} else if s[i].Price == s[j].Price {
		return s[i].Date < s[j].Date
	} else {
		return false
	}
}

func main() {

	shops := []Shop{
		{1, "222"}, {7, "222"}, {3, "222"},
		{4, "222"}, {5, "2018/1/21"}, {5, "2018/1/20"},
		{5, "2018/1/16"}, {5, "2018/1/19"},
		}

	sort.Sort(ByPriceData(shops))

	fmt.Println(shops)
}
```

```
$ go run test.go
[{7 222} {5 2018/1/16} {5 2018/1/19} {5 2018/1/20} {5 2018/1/21} {4 222} {3 222} {1 222}]
```

