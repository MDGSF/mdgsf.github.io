---
layout: post
title:  "[GO] State状态模式"
date:   2017-10-24
comments: true
categories: GO
tags: [Go]
description: ""
published: true
---


```go
package main

import (
    "fmt"
    "math/rand"
    "os"
    "time"
)

type GameContext struct {
    SecretNumber int
    Retries      int
    Won          bool
    Next         GameState
}

type GameState interface {
    executeState(*GameContext) bool
}

type StartState struct{}

func (s *StartState) executeState(c *GameContext) bool {
    c.Next = &AskState{}

    rand.Seed(time.Now().UnixNano())
    c.SecretNumber = rand.Intn(10)

    fmt.Println("Introduce a number of retries to set the difficulty:")
    fmt.Fscanf(os.Stdin, "%d\n", &c.Retries)

    return true
}

type AskState struct{}

func (a *AskState) executeState(c *GameContext) bool {
    fmt.Printf("Introduce a number between 0 and 10, you have %d tries left\n", c.Retries)

    var n int
    fmt.Fscanf(os.Stdin, "%d\n", &n)
    c.Retries = c.Retries - 1

    if n == c.SecretNumber {
        c.Won = true
        c.Next = &FinishState{}
    }

    if c.Retries == 0 {
        c.Next = &FinishState{}
    }

    return true
}

type FinishState struct{}

func (f *FinishState) executeState(c *GameContext) bool {
    if c.Won {
        c.Next = &WinState{}
    } else {
        c.Next = &LoseState{}
    }
    return true
}

type WinState struct{}

func (w *WinState) executeState(c *GameContext) bool {
    println("Congrats, you won")
    return false
}

type LoseState struct{}

func (l *LoseState) executeState(c *GameContext) bool {
    fmt.Printf("You lose, the corrent number is: %d\n", c.SecretNumber)
    return false
}

func main() {
    start := StartState{}
    game := GameContext{
        Next: &start,
    }

    for game.Next.executeState(&game) {

    }
}
```
