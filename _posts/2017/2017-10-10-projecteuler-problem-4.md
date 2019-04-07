---
layout: post
title:  "[GO][ProjectEuler] Problem 4"
date:   2017-10-10
comments: true
categories: GO
tags: [GO]
description: ""
published: true
---

https://projecteuler.net/problem=4

## Problem

A palindromic number reads the same both ways. The largest palindrome made from the product of two 2-digit numbers is 9009 = 91 × 99.

Find the largest palindrome made from the product of two 3-digit numbers.


## Answer

906609

## Code

```go
package main

import "fmt"

func bIsPalindromic(num int) bool {
	tNum := num
	rNum := 0
	for tNum > 0 {
		t := tNum % 10
		tNum = tNum / 10

		rNum = rNum*10 + t
	}

	if rNum == num {
		return true
	}

	return false
}

func main() {
	fmt.Println(bIsPalindromic(101))

	iMax := 0
	for i := 100; i < 1000; i++ {
		for j := 100; j < 1000; j++ {
			iPalindromic := i * j
			if bIsPalindromic(iPalindromic) {
				if iPalindromic > iMax {
					iMax = iPalindromic
				}
			}
		}
	}

	fmt.Println(iMax)
}
```

