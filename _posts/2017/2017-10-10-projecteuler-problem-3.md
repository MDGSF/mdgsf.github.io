---
layout: post
title:  "[GO][ProjectEuler] Problem 3"
date:   2017-10-10
comments: true
categories: GO
tags: [Go]
description: ""
published: true
---

https://projecteuler.net/problem=3

## Problem

The prime factors of 13195 are 5, 7, 13 and 29.

What is the largest prime factor of the number 600851475143 ?


## Answer

6857

## Code

```go
package main

import "fmt"

func main() {
	//13195 5 7 13 29
	//600851475143

	iMaxFactor := 0
	num := 600851475143
	i := 2

	for {
		if i > num {
			break
		}

		if num%i == 0 {
			num = num / i
			iMaxFactor = i
			continue
		} else {
			i++
		}
	}

	fmt.Println(iMaxFactor)
}
```

