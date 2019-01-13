---
layout: post
title: "[Art][leetcode - C++] 191 Number of 1 Bits"
date: 2017-03-03
author: mdgsf
comments: true
categories: Art
tags: leetcode
description:
published: true #default true
---

[https://leetcode.com/problems/number-of-1-bits/description/](https://leetcode.com/problems/number-of-1-bits/description/)

## 题目

Write a function that takes an unsigned integer and returns the number of ’1' bits it has (also known as the Hamming weight).

For example, the 32-bit integer ’11' has binary representation 00000000000000000000000000001011, so the function should return 3.

## 题目翻译

## 题目解析

求一个数的二进制中有多少个1。

## 参考答案

```cpp
class Solution {
public:
    int hammingWeight(uint32_t n) {
        if(0==n) {
            return 0;
        }
        int iCount = 1;
        while (n = n & (n-1))
        {
            iCount++;
        }
        return iCount;
    }
};
```
