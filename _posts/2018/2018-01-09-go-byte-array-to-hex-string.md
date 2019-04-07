---
layout: post
title:  "[GO] 字符数组转化为16进制字符串"
date:   2018-01-09
comments: true
categories: GO
tags: [GO]
description: ""
published: true
---

```go
package main

import "log"
import "fmt"
import "strings"

func main() {
	var data []byte

	for i := 'a'; i <= 'z'; i++ {
		data = append(data, byte(i))
	}
	log.Println("data = ", data)

	msg := fmt.Sprintf("%x", data)
	log.Println("data = ", msg)
	log.Println("data = ", strings.ToUpper(msg))
}
```
