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

## 求和系列题目

[两数之和]({{ site.url }}/2017/07/19/leetcode-1-two-sum/)

[三数之和]({{ site.url }}/2019/04/06/leetcode-15-3sum/)

[四数之和]({{ site.url }}/2019/04/06/leetcode-18-4sum/)

[四数相加 II]({{ site.url }}/2019/04/06/leetcode-454-4sum-2/)

## 链接

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

给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。

你可以假设每种输入只会对应一个答案。但是，你不能重复利用这个数组中同样的元素。

示例:

给定 nums = [2, 7, 11, 15], target = 9

因为 nums[0] + nums[1] = 2 + 7 = 9

所以返回 [0, 1]

## 题目解析

### 方法一：暴力法

暴力求解，两重循环，遍历所有的可能性。时间复杂度 O(n^2)。

这个太慢了，会超出时间限制。

```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        for i, iv in enumerate(nums):
            j = i + 1
            while j < len(nums):
                if nums[i] + nums[j] == target:
                    return [i, j]
                j += 1
```

### 方法二：两头逼近法

先排个序，然后从数组的两端向中间逼近。

排序时间复杂度 O(nlogn)。逼近过程复杂度 O(n)

所以复杂度为 O(nlogn + n) = O(nlogn)

这题目要求返回的是下标，所以这种方法排序的时候下标就改变了。如果在把下标记录下来，就不是很方便了。

如果要求返回的是值的话，可以这么写：

```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        nums.sort()
        start = 0
        end = len(nums) - 1
        while start < end:
            if nums[start] + nums[end] == target:
                return [nums[start], nums[end]]
            elif nums[start] + nums[end] > target:
                end -= 1
            else:
                start += 1
```

### 方法三：查找哈希表

遍历一次，时间复杂度为 O(n)，分析如下。

nums = [2, 7, 11, 15], target = 9

先建立一个空的哈希表 d

遍历到 2 的时候，计算 peer = target - 2 = 9 - 2 = 7

然后到 d 中去查找 peer=7 是否存在，很明显不存在，那就执行

```
d[2] = 0
```

就是把 2 这个数字对应的下标 0 保存到哈希表 d 中去。

然后在继续遍历，接下来遍历到 7，计算 peer = target - 7 = 2

然后到 d 中去查找 peer=2 是否存在，发现存在。返回结果。

参考代码：

```python
class Solution:
    def twoSum(self, nums: List[int], target: int) -> List[int]:
        d = {}
        for i, v in enumerate(nums):
            peer = target - v
            if peer in d:
                return [i, d[peer]]
            else:
                d[v] = i
```

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
