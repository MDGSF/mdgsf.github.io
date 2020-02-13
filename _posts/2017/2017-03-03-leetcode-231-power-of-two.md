---
layout: post
title: "[算法学习][leetcode] 231 Power of Two"
date: 2017-03-03
author: mdgsf
comments: true
categories: Art
tags: [Leetcode]
description:
published: true #default true
---

[https://leetcode.com/problems/power-of-two/description/](https://leetcode.com/problems/power-of-two/description/)

## 题目

Given an integer, write a function to determine if it is a power of two.

## 题目翻译

## 题目解析

判断一个数是不是2的幂。

## 参考答案

```cpp
class Solution {
public:
    bool isPowerOfTwo(int n) {
        if(n<=0)
        {
            return false;
        }

        return (n&(n-1)) == 0;
    }
};
```
