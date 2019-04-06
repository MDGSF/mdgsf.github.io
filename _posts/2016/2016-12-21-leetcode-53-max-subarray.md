---
layout: post
title: "[Art][leetcode] 53 Maximum Subarray"
date: 2016-12-21
author: mdgsf
comments: true
categories: Art
tags: [Leetcode]
description:
published: true #default true
---

[https://leetcode.com/problems/maximum-subarray/description/](https://leetcode.com/problems/maximum-subarray/description/)

## 题目

Find the contiguous subarray within an array (containing at least one number) which has the largest sum.

For example, given the array [-2,1,-3,4,-1,2,1,-5,4],

the contiguous subarray [4,-1,2,1] has the largest sum = 6.

## 题目翻译

## 题目解析

## 参考答案

```cpp
int maxSubArray(int* nums, int numsSize) {
    int i;
    int iSum = 0;
    int iAns = -100000000;
    for (i = 0; i < numsSize; i++)
    {
        iSum += nums[i];
        if(iSum > iAns)
        {
            iAns = iSum;
        }
        
        if(iSum < 0)
        {
            iSum = 0;
        }
    }
    
    return iAns;
}
```

iSum 用来保存当前计算的子数组的和

iAns 用来保存最大的子数组的和

如果 iSum < 0, 则这个子数组必定不能作为其它子数组的前缀，所以直接抛弃。

假设 A 为 iSum < 0 的一个子数组， B 为 iSum >= 0 的一个子数组。

<img src="{{ site.url }}/images/201612/21_01.png" alt="21_01" />

上图这种情况，B作为A的前缀，则B一定被**先计算过**。当A被抛弃时，B的结果已经被保存到iAns中过了。


<img src="{{ site.url }}/images/201612/21_02.png" alt="21_02" />

而这种A作为B的前缀，在计算A的时候A就已经被抛弃了。
