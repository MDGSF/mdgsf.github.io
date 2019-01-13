---
layout: post
title:  "[GO] import"
date:   2017-10-13
comments: true
categories: GO
tags: GO 
description: ""
published: true
---

## 1. 相对路径

import “./model” //当前文件同一目录的model目录，但是不建议这种方式来import

## 2. 绝对路径

import “shorturl/model” //加载gopath/src/shorturl/model模块

上面展示了一些import常用的几种方式，但是还有一些特殊的import，让很多新手很费解，下面我们来一一讲解一下
到底是怎么一回事

## 1. 点操作

我们有时候会看到如下的方式导入包

```go
import(
    . "fmt"
)
```

这个点操作的含义就是这个包导入之后在你调用这个包的函数时，你可以省略前缀的包名，也就是前面你调
用的fmt.Println("hello world")可以省略的写成Println("hello world")

## 2. 别名操作
别名操作顾名思义我们可以把包命名成另一个我们用起来容易记忆的名字

```go
import(
    f "fmt"
)
```

别名操作的话调用包函数时前缀变成了我们的前缀，即f.Println("hello world")

## 3. _操作

这个操作经常是让很多人费解的一个操作符，请看下面这个import

```go
import (
    "database/sql"
    _ "github.com/ziutek/mymysql/godrv"
)
```

_操作其实是引入该包，而不直接使用包里面的函数，而是调用了该包里面的init函数。


