---
layout: post
title: "[Art][动态规划] 总结"
date: 2019-01-26
author: mdgsf
comments: true
categories: Art
tags: [Art,Algorithm,动态规划]
description:
published: true #default true
---

## 四个要点

1. 递归 + 记忆化 -> 递推

2. 状态的定义： opt[n]，dp[n]，fib[n]

3. 状态转移方程： opt[n] = best_of(opt[n-1], opt[n-2], ...)

4. 最优子结构

## 链接

[斐波那契数列]({{ site.url }}/2019/01/26/algorithm-dp-fib/)

[爬楼梯]({{ site.url }}/2019/01/27/algorithm-dp-climb-stairs/)

[计算路径数]({{ site.url }}/2019/01/27/algorithm-dp-count-the-path/)

[游船费问题]({{ site.url }}/2016/12/21/algorithm-pleasure-boat/)

[三角形的最小路径和]({{ site.url }}/2019/03/09/algorithm-dp-triangle/)
