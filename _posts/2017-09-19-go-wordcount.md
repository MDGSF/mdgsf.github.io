---
layout: post
title:  "[GO] word count"
date:   2017-09-19
comments: true
categories: GO
tags: GO 
description: ""
published: true
---



```go
func WordCount(s string) map[string]int {
	
	m := make(map[string]int)
	
	words := strings.Fields(s)
	for _, v := range words {
		_, ok := m[v]
		if ok {
			m[v] = m[v] + 1
		} else {
			m[v] = 1
		}
	}
	return m
}
```