---
layout: post
title:  "[GO pattern] Time Functions"
date:   2018-01-17
comments: true
categories: GO
tags: [GO]
description: ""
published: true
---


```go
package main

import (
	"log"
	"math/big"
	"time"
)

// Duration 测量程序运行时间
func Duration(invocation time.Time, name string) {
	elapsed := time.Since(invocation)
	log.Printf("%s lasted %s", name, elapsed)
}

func BigIntFactorial(x big.Int) *big.Int {
	defer Duration(time.Now(), "IntFactorial")

	y := big.NewInt(1)
	for one := big.NewInt(1); x.Sign() > 0; x.Sub(&x, one) {
		y.Mul(y, &x)
	}

	return x.Set(y)
}

func main() {
	BigIntFactorial(*big.NewInt(10000))
}
```


[https://github.com/tmrts/go-patterns/blob/master/profiling/timing.md](https://github.com/tmrts/go-patterns/blob/master/profiling/timing.md)


