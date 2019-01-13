---
layout: post
title:  "[GO][ProjectEuler] Problem 1"
date:   2017-10-10
comments: true
categories: GO
tags: GO 
description: ""
published: true
---

https://projecteuler.net/problem=1

## Problem

If we list all the natural numbers below 10 that are multiples of 3 or 5, we get 3, 5, 6 and 9. The sum of these multiples is 23.

Find the sum of all the multiples of 3 or 5 below 1000.


## Answer

233168


## Code

```go
package main

import "fmt"

func main() {
	sum := 0
	for i := 1; i < 10; i++ {
		if i%3 == 0 || i%5 == 0 {
			sum += i
		}
	}
	fmt.Println(sum)
}
```
