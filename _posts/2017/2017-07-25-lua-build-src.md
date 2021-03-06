---
layout: post
title: "[Lua-pil] 编译源码"
date: 2017-07-25
author: mdgsf
comments: true
categories: lua
tags: [Lua]
description:
published: true #default true
---

## 介绍

官网地址：

<a href="http://www.lua.org/home.html" target="_blank">http://www.lua.org/home.html</a>

Lua是脚本语言，可以自己运行，可以嵌入C语言运行，也可以把C语言嵌入Lua运行。

Lua全部都是用C语言实现的，可以到官网去下载源码，自己编译，编译过程很简单，没什么错误。

源码目录结构：

<img src="{{ site.url }}/images/2017/07/25_01.png" alt="25_01" />

## 编译源码

需要在VS中新建一个VC++解决方案，在该解决方案下建3个项目，分别是**lua库项目**，**lua编译器项目**，**lua解释器项目**。

<img src="{{ site.url }}/images/2017/07/25_02.png" alt="25_02" />

Lua53 是库项目，生成Lua53.lib。

Lua 是解释器项目，生成Lua.exe。

Luac 是编译器项目，生成Luac.exe。

最终要生成的文件如下：

<img src="{{ site.url }}/images/2017/07/25_03.png" alt="25_03" />

### 建立Lua53项目

打开VS2013(其他版本亦可)，新建VisuallC++ ->win32控制台应用程序，修改项目名称为Lua53，然后再点击确定。

点击下一步，选择静态库，空项目，点击完成。

将src目录下除了lua.c和luac.c其他所有文件加入lua53工程下的源文件目录

### 建立Luac编译器项目

打开VS2013(其他版本亦可)，新建VisuallC++ ->win32控制台应用程序，修改项目名称为Luac，然后再点击确定。

点击下一步，选择控制台应用程序，空项目，点击完成。

将src目录下除了lua.c其他所有文件加入lua53工程下的源文件目录

### 建立Lua解释器项目

打开VS2013(其他版本亦可)，新建VisuallC++ ->win32控制台应用程序，修改项目名称为Lua，然后再点击确定。

点击下一步，选择控制台应用程序，空项目，点击完成。

将src目录下除了luac.c其他所有文件加入lua53工程下的源文件目录

## Lua的使用

最终我们会得到Lua53.lib, Luac.exe, Lua.exe。那要怎么使用呢？

Lua.exe 是一个交互式的命令行解释器，可以直接双击打开。

<img src="{{ site.url }}/images/2017/07/25_04.png" alt="25_04" />

Luac.exe 是编译器。

新建一个Hello.lua，执行

<img src="{{ site.url }}/images/2017/07/25_05.png" alt="25_05" />

Lua53.lib 是一个库文件，如果想要在C语言中嵌入Lua的话，就需要把这个文件加入到C语言的项目中。

## 参考链接

<a href="http://www.jianshu.com/p/2f649d9aef6d" target="_blank">http://www.jianshu.com/p/2f649d9aef6d</a>
