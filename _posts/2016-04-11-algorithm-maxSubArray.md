---
layout: post
title:  "[数组] 最大子数组之和"
date:   2016-04-11
comments: true
categories: Art
tags: [C]
description:
published: true
---

题目：输入一个整形数组，数组里有正数也有负数。数组中连续的一个或多个整数组成一个子数组，每个子数组都有一个和。求所有子数组的和的最大值。要求时间复杂度为O(n)。

例如输入的数组为1, -2, 3, 10, -4, 7, 2, -5，和最大的子数组为3, 10, -4, 7, 2，因此输出为该子数组的和18。

### 方法一 暴力枚举法

时间复杂度 O(n^2)

```cpp
#include <stdio.h>
#include <iostream>
using namespace std;

const int INF = 0x7fffffff;

int iMaxSubArray(int a[], int n, int & left, int & right)
{
    int iMax = -INF;
    int iSum = 0;
    for (int i = 0; i < n; i++) {
        iSum = 0;
        for (int j = i; j < n; j++) {
            iSum += a[j];
            if(iSum > iMax) {
                iMax = iSum;
                left = i;
                right = j;
            }
        }
    }
    return iMax;
}

int main()
{
    printf("%d %d\n", INF, -INF);

    int iLeft = -1;
    int iRight = -1;
    int a[] = {1, -2, 3, 10, -4, 7, 2, -5};
    int iMax = iMaxSubArray(a, 8, iLeft, iRight);
    printf("%d %d %d\n", iMax, iLeft, iRight);
    return 0;
}
```

### 方法二：分支界定

这里再介绍一种更高效的算法，时间复杂度为O(nlogn)。这是个分治的思想，解决复杂问题我们经常使用的一种思维方法——分而治之。
而对于此题，我们把数组A[1..n]分成两个相等大小的块：A[1..n/2]和A[n/2+1..n]，最大的子数组只可能出现在三种情况：

1. A[1..n]的最大子数组和A[1..n/2]最大子数组相同；
2. A[1..n]的最大子数组和A[n/2+1..n]最大子数组相同；
3. A[1..n]的最大子数组跨过A[1..n/2]和A[n/2+1..n]

前两种情况的求法和整体的求法是一样的，因此递归求得。

第三种，我们可以采取的方法也比较简单，沿着第n/2向左搜索，直到左边界，找到最大的和maxleft，以及沿着第n/2+1向右搜索找到最大和maxright，那么总的最大和就是maxleft+maxright。

而数组A的最大子数组和就是这三种情况中最大的一个。


### 方法三：动态规划

设sum[i]为以第i个元素结尾且和最大的连续子数组。假设对于元素i，
所有以它前面的元素结尾的子数组的长度都已经求得，那么以第i个元素结尾且和最大的连续子数组实际上，
要么是以第i-1个元素结尾且和最大的连续子数组加上这个元素，要么是只包含第i个元素，
即sum[i] = max(sum[i-1] + a[i], a[i])。可以通过判断sum[i-1] + a[i]是否大于a[i]来做选择，
而这实际上等价于判断sum[i-1]是否大于0。由于每次运算只需要前一次的结果，
因此并不需要像普通的动态规划那样保留之前所有的计算结果，
只需要保留上一次的即可，因此算法的时间和空间复杂度都很小。

```cpp
#include <stdio.h>

int iMaxSubArray(int a[], int n)
{
    int iResult = a[0];
    int iSum = a[0];

    for (int i = 1; i < n; i++)
    {
        if(iSum > 0)
            iSum += a[i];
        else
            iSum = a[i];

        if(iSum > iResult)
            iResult = iSum;
    }

    return iResult;
}

int main()
{
    int a[] = {1, -2, 3, 10, -4, 7, 2, -5};
    int iMax = iMaxSubArray(a, 8);
    printf("%d\n", iMax);
    return 0;
}
```
