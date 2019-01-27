---
layout: post
title: "[Art][动态规划] 爬楼梯"
date: 2019-01-27
author: mdgsf
comments: true
categories: Art
tags: [Art,Algorithm,动态规划]
description:
published: true #default true
---

## 题目

[https://leetcode-cn.com/problems/climbing-stairs/](https://leetcode-cn.com/problems/climbing-stairs/)

假设你正在爬楼梯。需要 n 阶你才能到达楼顶。

每次你可以爬 1 或 2 个台阶。你有多少种不同的方法可以爬到楼顶呢？

注意：给定 n 是一个正整数。

示例 1：

```
输入： 2
输出： 2
解释： 有两种方法可以爬到楼顶。
1.  1 阶 + 1 阶
2.  2 阶
```

示例 2：

```
输入： 3
输出： 3
解释： 有三种方法可以爬到楼顶。
1.  1 阶 + 1 阶 + 1 阶
2.  1 阶 + 2 阶
3.  2 阶 + 1 阶
```

## 递推公式

```
F[n] = F[n-1] + F[n-2]
F[1] = 1
F[2] = 2
```

所以这个其实是和斐波那契数列一样的。

## 方法一、简单递归

时间复杂度：O(2^n)

大量重复计算，会超时。

```golang
func climbStairs(n int) int {
	if n == 1 {
		return 1
	} else if n == 2 {
		return 2
	}
	return climbStairs(n-1) + climbStairs(n-2)
}
```

## 方法二、递归 + 记忆化

时间复杂度：O(n)

```golang
func climbStairs(n int) int {
	m := make(map[int]int)
	return inner(n, m)
}

func inner(n int, m map[int]int) int {
	if n <= 2 {
		return n
	}
	if _, ok := m[n]; !ok {
		m[n] = inner(n-1, m) + inner(n-2, m)
	}
	return m[n]
}
```

## 方法三、递推

时间复杂度：O(n)

```golang
func climbStairs(n int) int {
	if n <= 2 {
		return n
	}
	i, j := 1, 2
	for idx := 3; idx <= n; idx++ {
		i, j = j, i+j
	}
	return j
}
```
