---
layout: post
title:  "[GO] tcp 传输文件"
date:   2017-10-14
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
    "io"
    "net"
    "os"
)

func server() {
    ln, err := net.Listen("tcp", ":9999")
    if err != nil {
        fmt.Println(err)
        return
    }

    for {
        c, err := ln.Accept()
        if err != nil {
            fmt.Println(err)
            continue
        }

        go handleServerConnection(c)
    }
}

func handleServerConnection(c net.Conn) {
    f, err := os.OpenFile("./test/aa.pdf", os.O_WRONLY|os.O_CREATE, 0666)
    if err != nil {
        fmt.Println(err)
        return
    }
    defer f.Close()

    io.Copy(f, c)

    fmt.Println("server copy end")

    c.Close()
}

func client() {
    c, err := net.Dial("tcp", "127.0.0.1:9999")
    if err != nil {
        fmt.Println(err)
        return
    }

    fileName := "test.pdf"
    f, err := os.Open(fileName)
    if err != nil {
        fmt.Println("err opening file")
        return
    }

    fileInfo, err := os.Lstat(fileName)
    if err != nil {
        fmt.Println("err Lstat")
        return
    }
    fmt.Println(fileInfo.Size())

    written, err := io.CopyN(c, f, fileInfo.Size())
    if written != fileInfo.Size() || err != nil {
        fmt.Println("error copy")
        return
    }

    fmt.Println("client copy end", written)

    c.Close()
}

func main() {
    go server()
    go client()

    var input string
    fmt.Scanln(&input)
}
```
