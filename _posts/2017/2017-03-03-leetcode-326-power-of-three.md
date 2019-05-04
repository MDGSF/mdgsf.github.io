---
layout: post
title: "[Art][leetcode] 326 Power of Three"
date: 2017-03-03
author: mdgsf
comments: true
categories: Art
tags: [Leetcode]
description:
published: true #default true
---

[https://leetcode.com/problems/power-of-three/description/](https://leetcode.com/problems/power-of-three/description/)

## 题目

 Given an integer, write a function to determine if it is a power of three.

**Follow up:**
Could you do it without using any loop / recursion?

## 题目翻译

## 题目解析

判断一个数是不是3的幂。

## 参考答案

```cpp
class Solution {
public:
    bool isPowerOfThree(int n) {
        if(n <= 0) return false;

        //3 9 27 81
        //big3 % n == 0
        //big3 = 3^k
        //k = log3(maxint);
        const int maxint = 0x7fffffff;
        int k = log(maxint)/log(3);
        int big3 = pow(3, k);

        return big3 % n == 0;
    }
};
```
