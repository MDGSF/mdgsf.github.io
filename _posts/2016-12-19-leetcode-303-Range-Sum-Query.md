---
layout: post
title: "[Art][leetcode - C++] 303 Range Sum Query - Immutable"
date: 2016-12-19
author: mdgsf
comments: true
categories: Art
tags: leetcode
description:
published: true #default true
---

[https://leetcode.com/problems/range-sum-query-immutable/description/](https://leetcode.com/problems/range-sum-query-immutable/description/)

## 题目

Given an integer array nums, find the sum of the elements between indices i and j (i ≤ j), inclusive.

**Example:**

```
Given nums = [-2, 0, 3, -5, 2, -1]

sumRange(0, 2) -> 1
sumRange(2, 5) -> -1
sumRange(0, 5) -> -3
```

**Note:**

1.You may assume that the array does not change.

2.There are many calls to sumRange function.

## 题目翻译

## 题目解析

## 参考答案

```cpp
class NumArray {
public:
    vector<int> sums;
    NumArray(vector<int> &nums) {
        int i;
        int iSize = nums.size();
        sums = nums;
        for (i = 1; i < iSize; i++)
        {
            sums[i] += sums[i-1];
        }
    }

    int sumRange(int i, int j) {
        printf("i = %d, j = %d, ", i, j);
        if(i == 0)
        {
            printf("result = %d\n", sums[j]);
            return sums[j];
        }
        else
        {
            printf("result = %d\n", sums[j] - sums[i-1]);
            return sums[j] - sums[i-1];
        }
    }
};


// Your NumArray object will be instantiated and called as such:
// NumArray numArray(nums);
// numArray.sumRange(0, 1);
// numArray.sumRange(1, 2);
```
