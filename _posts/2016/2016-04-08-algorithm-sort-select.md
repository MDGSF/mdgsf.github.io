---
layout: post
title: "[排序] 选择排序"
date: 2016-04-08
author: mdgsf
comments: true
categories: Art
tags: [C,Sort]
description:
published: true #default true
---


直接选择排序和直接插入排序类似，都将数据分为有序区和无序区，所不同的是直接播放排序是将无序区的第一个元素直接插入到有序区以形成一个更大的有序区，而直接选择排序是从无序区选一个最小的元素直接放到有序区的最后。


设数组为a[0…n-1]。

1. 初始时，数组全为无序区为a[0..n-1]。令i=0
2. 在无序区a[i…n-1]中选取一个最小的元素，将其与a[i]交换。交换之后a[0…i]就形成了一个有序区。
3. i++并重复第二步直到i==n-1。排序完成。

```cpp
#include <stdio.h>
#include <iostream>
using namespace std;

void vPrint(int a[], int n)
{
    for (int i = 0; i < n; i++)
    {
        printf("%d ", a[i]);
    }
    printf("\n");
}

void selectSort(int a[], int n)
{
    int i, j, iMinIndex;
    for (i = 0; i < n; i++)
    {
        iMinIndex = i;
        for (j = i + 1; j < n; j++) {
            if(a[j] < a[iMinIndex]) {
                iMinIndex = j;
            }
        }

        swap(a[i], a[iMinIndex]);
    }
}

int main()
{
    int a[] = {24,21,132,4531,1,23,352,22,1234,46436};
    vPrint(a, 10);
    selectSort(a, 10);
    vPrint(a, 10);
    return 0;
}
```

### 链接

[白话经典算法系列之四 直接选择排序](http://blog.csdn.net/morewindows/article/details/6671824)
