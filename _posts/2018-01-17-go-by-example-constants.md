---
layout: post
title:  "[Go By Example] Constants"
date:   2018-01-17
comments: true
categories: GoByExample
tags: GO
description: ""
published: true
---

```go
package main

import (
	"fmt"
	"math"
)

const s string = "constant"

func main() {
	fmt.Println(s)

	const n = 5000000

	const d = 3e20 / n
	fmt.Println(d)

	fmt.Println(int64(d))

	fmt.Println(math.Sin(n))
}
```

```
$ go run test.go
constant
6e+13
60000000000000
-0.9765424686570829
```

[https://gobyexample.com/constants](https://gobyexample.com/constants)