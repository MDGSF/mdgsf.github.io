---
layout: post
title:  "[GO][ProjectEuler] Problem 5"
date:   2017-10-10
comments: true
categories: GO
tags: [Go]
description: ""
published: true
---

https://projecteuler.net/problem=5

## Problem

2520 is the smallest number that can be divided by each of the numbers from 1 to 10 without any remainder.

What is the smallest positive number that is evenly divisible by all of the numbers from 1 to 20?


## Answer

232792560

## Code

```go
package main

import (
	"fmt"
)

func bIsPrimer(num int) bool {
	for i := 2; i < num; i++ {
		if num%i == 0 {
			return false
		}
	}
	return true
}

func getFactor(num int) []int {
	var s []int
	i := 2
	for {
		if i >= num {
			break
		}

		if num%i == 0 {
			s = append(s, i)
			for num > 0 && num%i == 0 {
				num /= i
			}
			i++
			continue
		} else {
			i++
		}
	}

	return s
}

func main() {
	result := 2

	for i := 3; i <= 20; i++ {
		if bIsPrimer(i) {
			result = result * i
		} else if result%i == 0 {
			continue
		} else {
			s := getFactor(i)
			for _, v := range s {
				result = result * v
			}
		}
	}

	fmt.Println(result)
}
```
