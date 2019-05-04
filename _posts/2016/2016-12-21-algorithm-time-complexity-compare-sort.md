---
layout: post
title: "[时间复杂度] 比较排序的时间复杂度的下界是 nlogn"
date: 2016-12-21
author: mdgsf
comments: true
categories: Art
tags: [Art,Algorithm]
description:
published: true #default true
---

证明：

```
1,2,3,4,.....,n   sort
他们的全排列是 n!
而排序好的1~n则是全排列中的一种。

每一次比较 i < j, 最优的情况则是去掉了 n! 种情况中的一半, n!/2

第0次， n!
第1次，n!/2
第2次，(n!/2)/2 = (n!)/(2^2)
...
第k次, (n!)/(2^k)

当  (n!)/(2^k) = 1时，算法结束
n! = 2^k，然后两边取以2为底的对数
log(n!) = k，而 n! 约等于 n^n，并且 n^n > n!
log(n^n) = k
nlog(n) = k

所以时间复杂度为 nlogn
```
