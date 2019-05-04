---
layout: post
title: "[时间复杂度] 求一个数的全部的约数"
date: 2016-12-21
author: mdgsf
comments: true
categories: Art
tags: [Art,Algorithm]
description:
published: true #default true
---

求一个数的全部的约数，时间复杂度为 O(n^(1/2))，就是n的二分之一次方。

比如求100的全部约数。

暴力解法：从1遍历到100.

但是当我们遍历到2的时候，就可以知道100的另一个约数是50了。

所以没有必要全部遍历，只要遍历到 sqrt(100) 就能够找到全部的约数了。
