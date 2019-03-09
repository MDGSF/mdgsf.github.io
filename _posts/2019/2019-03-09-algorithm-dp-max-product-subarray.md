---
layout: post
title: "[Art][动态规划] 乘积最大子序列"
date: 2019-03-09
author: mdgsf
comments: true
categories: Art
tags: [Art,Algorithm,动态规划]
description:
published: true #default true
---

## 题目

[https://leetcode-cn.com/problems/maximum-product-subarray/](https://leetcode-cn.com/problems/maximum-product-subarray/)

## 解析

我们先尝试定义状态 DP[i] 为从 0 到 i 中子序列的最大乘积，一定包括第 i 个节点。

那么我们尝试定义状态转移方程： DP[i] = DP[i-1] * nums[i]

如果数组中的数字全部都是正数，那么没有任何问题。

可是 nums[i] 可能是负数，这个时候，方程便无法满足了。

当 nums[i] 为负数的时候，只要 DP[i-1] 也是负数的话，那就有可能是最大乘积，所以同一个节点我们需要同时保存正数和负数。

我们尝试重新定义状态：**DP[i][2]**，即 **DP[i][0]** 和 **DP[i][1]**。

DP[i][0] 表示从 0 到 i 中子序列`正`的最大乘积

DP[i][1] 表示从 0 到 i 中子序列`负`的最大乘积

那么状态转移方程如下：

```
DP[i][0] = if (nums[i] >= 0)       //当 nums[i] 是正数
               DP[i-1][0]*nums[i]  //乘以上一个状态的正数
           else                    //当 nums[i] 是负数
               DP[i-1][1]*nums[i]  //乘以上一个状态的负数
DP[i][1] = if (nums[i] >= 0) 
               DP[i-1][1]*nums[i]
           else 
               DP[i-1][0]*nums[1]
```

```
DP[i][0] = max(DP[i-1][0]*nums[i], DP[i-1][1]*nums[i], nums[i]) //取正数的最大值
DP[i][1] = min(DP[i-1][1]*nums[i], DP[i-1][0]*nums[1], nums[i]) //取负数中的最小值
```

最后因为我们需要的是最大的乘积，所以结果就是

Max(DP[0][0], DP[1][0], ..., DP[i][0])

## 代码 1

```golang
/*
状态: DP[i][2]
DP[i][0]:表示从 0 -> i (包括第 i 个节点) 的正的最大值
DP[i][1]:表示从 0 -> i (包括第 i 个节点) 的负的最大值，也就是最小值

DP[i][0] = if (nums[i] >= 0) DP[i-1][0]*nums[i]
           else DP[i-1][1]*nums[i]
DP[i][1] = if (nums[i] >= 0) DP[i-1][1]*nums[i]
           else DP[i-1][0]*nums[1]

结果： Max(DP[i][0], DP[i-1][0], ..., DP[0][0])
*/
func maxProduct(nums []int) int {
    if len(nums) == 0 {
        return 0
    }
    
    DP := make([][]int, len(nums))
    for k := range DP {
        DP[k] = make([]int, 2)
    }
    
    DP[0][0], DP[0][1] = nums[0], nums[0]
    res := DP[0][0]
    for i := 1; i < len(nums); i++ {
        DP[i][0] = max(DP[i-1][0]*nums[i], DP[i-1][1]*nums[i], nums[i])
        DP[i][1] = min(DP[i-1][1]*nums[i], DP[i-1][0]*nums[i], nums[i])
        res = max(res, DP[i][0])
    }
    return res
}

func max(arg ...int) int {
    m := arg[0]
    for i := 1; i < len(arg); i++ {
        if arg[i] > m {
            m = arg[i]
        }
    }
    return m
}

func min(arg ...int) int {
    m := arg[0]
    for i := 1; i < len(arg); i++ {
        if arg[i] < m {
            m = arg[i]
        }
    }
    return m
}
```

## 代码 2

用滚动数组优化下空间

```golang
func maxProduct(nums []int) int {
    if len(nums) == 0 {
        return 0
    }
    
    var DP [2][2]int
    DP[0][0], DP[0][1] = nums[0], nums[0]
    res := DP[0][0]
    for i := 1; i < len(nums); i++ {
        x, y := i % 2, (i - 1) % 2
        DP[x][0] = max(DP[y][0]*nums[i], DP[y][1]*nums[i], nums[i])
        DP[x][1] = min(DP[y][1]*nums[i], DP[y][0]*nums[i], nums[i])
        res = max(res, DP[x][0])
    }
    return res
}
```