---
layout: post
title:  "[算法学习] 面试题收集1"
date:   2018-12-23
comments: true
categories: Art
tags: [Interview,Algorithm]
description: ""
published: true
---

## 题目描述

给你一个数列和一个随机数字 A，你可以把数列中的每一个数字加上这个随机数字 A 或者减去这个随机数字 A，必须加上或者减去，不能不操作。问：要怎么样才能让操作之后的数列中的最大值和最小值间的距离最小。

举个例子：

```
数列：1 2 3 4 5
随机数字A：1
```

```
1 + 1 = 2
2 + 1 = 3
3 + 1 = 4
4 - 1 = 3
5 - 1 = 4

于是我就得到
新的数列：2 3 4 3 4
最大值和最小值的距离就是 2
```

```
1 + 1 = 2
2 + 1 = 3
3 + 1 = 4
4 + 1 = 5
5 + 1 = 6

于是我就得到
新的数列：2 3 4 5 6
最大值和最小值的距离就是 4
```

[https://github.com/MDGSF/GoPractice/blob/master/a/1/main.go](https://github.com/MDGSF/GoPractice/blob/master/a/1/main.go)
