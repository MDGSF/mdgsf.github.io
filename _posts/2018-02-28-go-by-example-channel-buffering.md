---
layout: post
title:  "[Go By Example] Channel Buffering"
date:   2018-02-28
comments: true
categories: GoByExample
tags: GO
description: ""
published: true
---

```go
package main

import "fmt"

func main() {
    messages := make(chan string, 2)

    messages <- "buffered"
    messages <- "channel"

    fmt.Println(<-messages)
    fmt.Println(<-messages)
}
```

```
$ go run test.go
buffered
channel
```

[https://gobyexample.com/channel-buffering](https://gobyexample.com/channel-buffering)

