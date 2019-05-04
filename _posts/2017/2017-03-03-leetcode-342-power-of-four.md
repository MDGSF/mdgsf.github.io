---
layout: post
title: "[Art][leetcode] 342 Power of Four"
date: 2017-03-03
author: mdgsf
comments: true
categories: Art
tags: [Leetcode]
description:
published: true #default true
---

[https://leetcode.com/problems/power-of-four/description/](https://leetcode.com/problems/power-of-four/description/)

## 题目

Given an integer (signed 32 bits), write a function to check whether it is a power of 4.

**Example:**
Given num = 16, return true. Given num = 5, return false.

**Follow up:** Could you solve it without loops/recursion?

## 题目翻译

## 题目解析

判断一个数是不是4的幂。

## 参考答案

```cpp
class Solution {
public:
    bool isPowerOfFour(int num) {

        //2 8  32 false   2=0010  8=1000        32=0010,0000
        //4 16 64 true    4=0100  16=0001,0000  64=0100,0000

        //5=0101

        if(num <= 0) return false;

        return (((num&(num-1))==0) && (num&0x55555555));
    }
};
```
