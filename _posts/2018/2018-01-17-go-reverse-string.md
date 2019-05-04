---
layout: post
title:  "[GO] reverse string"
date:   2018-01-17
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
    str := "abcdef"
    rstr := reverse(str)
    fmt.Println(rstr)
}

func reverse(str string) string {
    runes := []rune(str)
    l := 0
    r := len(runes) - 1
    for l <= r {
        runes[l], runes[r] = runes[r], runes[l]
        l++
        r--
    }
    return string(runes)
}
```

```
$ go run test.go
fedcba
```
