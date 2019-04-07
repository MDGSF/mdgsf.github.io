---
layout: post
title:  "[GO] Windows下安装go"
date:   2017-09-15
comments: true
categories: GO
tags: [GO]
description: ""
published: true
---

# Windows下安装go

## 配置环境变量

GOROOT：Go的安装目录

GOPATH：用于存放Go语言Package的目录，这个目录不能在Go的安装目录中

GOBIN：Go二进制文件存放目录，写成%GOROOT%\bin就好

GOOS：操作系统

GOARCH：指定系统环境，i386表示x86，amd64表示x64

PATH：需要将%GOBIN%加在PATH变量的最后，方便在命令行下运行Go

## 查看版本

```go
E:\Go\code>go version
go version go1.9 windows/amd64
```

## helloworld.go

```go
package main

import (
    "fmt"
)

func main() {
    fmt.Println("Hello World!")
}
```

执行命令 go run helloworld.go
