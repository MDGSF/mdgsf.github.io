---
layout: post
title:  "copy on write"
date:   2018-12-13
comments: true
categories: miscellanea
tags: cow, copy on write
description:
published: true
---

实现 copy on write 的两个方法

#### 1. 自己实现一个有 copy on write 的数据结构，比如：

https://github.com/google/btree

#### 2. 使用 fork()

https://github.com/MDGSF/CPractice/blob/master/forkDemo/main.cpp
