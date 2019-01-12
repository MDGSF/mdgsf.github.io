---
layout: post
title:  "[GO] map用来保存函数"
date:   2017-10-20
comments: true
categories: GO
tags: GO 
description: ""
published: true
---


```go
package main

import (
    "fmt"
)

func f(p string) {
    fmt.Println("func f parameter:", p)
}

func g(p string) {
    fmt.Println("func g parameter:", p)
}

func h(p string, q int) {
    fmt.Println("func h parameter:", p, q)
}

func main() {
    m := map[int]func(string){}
    m[1] = f
    m[2] = g

    for k, v := range m {
        fmt.Printf("%d:", k)
        v("test")
    }

    d := map[string]interface{}{
        "f": f,
        "g": g,
        "h": h,
    }
    for k, v := range d {
        switch k {
        case "f":
            v.(func(string))("astring")
        case "g":
            v.(func(string))("gString")
        case "h":
            v.(func(string, int))("hString", 42)
        }
    }
}
```


