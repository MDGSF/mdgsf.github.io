---
layout: post
title: "[Art][时间复杂度] C++ vector 的时间复杂度分析"
date: 2016-12-21
author: mdgsf
comments: true
categories: Art
tags: [Art,Algorithm]
description:
published: true #default true
---


```
vector的容量满了的时候，就从新分配一块2倍大小的空间，然后把
数据全部拷贝到新的空间，再把旧的空间释放。

一开始vector的空间大小为1，那么每次从新分配空间的时候为：
1 , 2, 4, 8, 16, 32, ....
2^0 + 2^1 + 2^2 + 2^3＋2^4 + .... + 2^(logn) 以2为底
这个式子算出来结果为 2 * 2^(logn) - 1 = 2n = O(n)
所以用vector保存了n个数的时候，时间复杂度为O(n)，所以对每个数字均摊来说，时间复杂度是O(1)
```




