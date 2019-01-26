---
layout: post
title: "[Art][动态规划] 斐波那契数列"
date: 2019-01-26
author: mdgsf
comments: true
categories: Art
tags: [Art,Algorithm,动态规划]
description:
published: true #default true
---

## 斐波那契数列

```
num: 0, 1, 1, 2, 3, 5, 8, 13, ...
idx: 0, 1, 2, 3, 4, 5, 6,  7, ...
```

就是前两个数相加等于第 3 个数。

希望实现一个函数，传入参数 idx 下标，返回对应的 num 数字。 

## 递推公式

```
fib[n] = fib[n-1] + fib[n-2]
fib[0] = 0
fib[1] = 1
```

## 方法一、简单递归

```golang
func fib(n int) int {
    if n <= 1 {
        return n
    }
    return fib(n-1) + fib(n-2)
}
```

时间复杂度： O(2^n)

那为什么时间复杂度是 O(2^n) 呢？

## 方法二、简单递归 + 记忆化

```golang
func fib(n int, m map[int]int) int {
    if n <= 1 {
        return n
    }
    if _, ok := m[n]; !ok {
        m[n] = fib(n-1, m) + fib(n-2, m)
    }
    return m[n]
}
```

时间复杂度：O(n)

## 方法三、递推

`递推`其实就只是把递归的代码修改为用循环实现。

`递推`的顺序是和递归正好相反的。

```golang
func fib(n int) int {
    if n <= 1 {
        return n
    }

    i, j := 0, 1
    for idx := 2; idx <= n; idx++ {
        i, j = j, i+j
    }
    return j
}
```

时间复杂度：O(n)
