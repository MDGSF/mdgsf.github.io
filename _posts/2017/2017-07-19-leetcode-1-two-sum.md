---
layout: post
title: "[Art][leetcode] 1 Two Sum"
date: 2017-07-19
author: mdgsf
comments: true
categories: Art
tags: [Leetcode]
description:
published: true #default true
---

[https://leetcode-cn.com/problems/two-sum/](https://leetcode-cn.com/problems/two-sum/)

[https://leetcode.com/problems/two-sum/description/](https://leetcode.com/problems/two-sum/description/)

## 题目

Given an array of integers, return indices of the two numbers such that they add up to a specific target.

You may assume that each input would have exactly one solution, and you may not use the same element twice.

Example:
Given nums = [2, 7, 11, 15], target = 9,

Because nums[0] + nums[1] = 2 + 7 = 9,
return [0, 1].

## 题目翻译

## 题目解析

## 参考答案

```cpp
#include <iostream>
#include <vector>
#include <map>
#include <stdio.h>
using namespace std;

class Solution {
public:
    vector<int> twoSum(vector<int>& nums, int target) {
        
        vector<int> vecResult;
        map<int, int> mapNumIndex;
        map<int, int>::iterator mapIter;
        
        for (int i = 0; i < nums.size(); i++)
        {
            int iNum = nums[i];
            int iPeerNum = target - iNum;
            int iIndex = i;

            mapIter = mapNumIndex.find(iPeerNum);
            if(mapIter != mapNumIndex.end())
            {
                vecResult.push_back(mapIter->second);
                vecResult.push_back(iIndex);
            }
            else
            {
                mapNumIndex.insert( std::pair<int, int>(iNum, iIndex) );
            }
        }

        return vecResult;

    }
};

int main()
{
    vector<int> nums;
    nums.push_back(2);
    nums.push_back(7);
    nums.push_back(11);
    nums.push_back(15);
    Solution oSolution;
    oSolution.twoSum(nums, 9);
    return 0;
}
```
