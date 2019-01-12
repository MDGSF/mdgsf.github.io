---
layout: post
title:  "[GO] A simple load balancer"
date:   2017-11-16
comments: true
categories: GO
tags: GO 
description: ""
published: true
---


```go
func Run() {
   in, out := make(chan *Work), make(chan *Work)
   for i := 0; i < NumWorkers; i++ {
       go worker(in, out)
   }
   go sendLotsOfWork(in)
   receiveLotsOfResults(out)
}
```
