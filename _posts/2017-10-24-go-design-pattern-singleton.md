---
layout: post
title:  "[GO] Singleton单例模式"
date:   2017-10-24
comments: true
categories: GO
tags: GO 
description: ""
published: true
---

## singleton.go

```go
package singleton

type Singleton interface {
    AddOne() int
}

type singleton struct {
    count int
}

var instance *singleton

func GetInstance() Singleton {
    if instance == nil {
        instance = new(singleton)
    }
    return instance
}

func (s *singleton) AddOne() int {
    s.count++
    return s.count
}
```

## singleton_test.go

```go
package singleton

import "testing"

func TestGetInstance(t *testing.T) {
    counter1 := GetInstance()
    if counter1 == nil {
        t.Error("expected pointer to Singleton after calling GetInstance(), not nil")
    }

    expectedCounter := counter1

    currentCount := counter1.AddOne()
    if currentCount != 1 {
        t.Errorf("After calling for the first time to count, count must be 1 but it is %d\n", currentCount)
    }

    counter2 := GetInstance()
    if counter2 != expectedCounter {
        t.Error("Expected same instance in counter2 but it got an different instance")
    }

    currentCount = counter2.AddOne()
    if currentCount != 2 {
        t.Errorf("After calling 'addone' using the second counter, the current count must be 2 but it is %d\n", currentCount)
    }
}
```

