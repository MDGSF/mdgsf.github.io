---
layout: post
title: "[Art][动态规划] 三角形的最小路径和"
date: 2019-03-09
author: mdgsf
comments: true
categories: Art
tags: [Art,Algorithm,动态规划]
description:
published: true #default true
---

## 题目

[https://leetcode-cn.com/problems/triangle/](https://leetcode-cn.com/problems/triangle/)

## 递推公式

```
DP[i, j] = min(DP[i+1, j], DP[i+1, j+1]) + Triangle[i, j]
```

Triangle[i, j] 是表示三角形中第 i 行，第 j 列的值。

DP[i, j] 是表示递归到第 i 行，第 j 列时的状态值。

该公式意思：当前节点的状态值为下一层邻接的两个节点中的较小值加上当前节点的值。

整个状态的初始值为三角形的最底下一层。

## 代码

```golang
func minimumTotal(triangle [][]int) int {
    row := len(triangle)
    for i := row - 2; i >= 0; i-- { //从倒数第二层开始向上遍历
        for j := 0; j < len(triangle[i]); j++ {
            triangle[i][j] += min(triangle[i+1][j], triangle[i+1][j+1])
        }
    }
    return triangle[0][0]
}

func min(a, b int) int {
    if a < b {
        return a
    }
    return b
}
```