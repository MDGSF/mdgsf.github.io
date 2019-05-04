---
layout: post
title: "[排序] 插入排序"
date: 2016-04-08
author: mdgsf
comments: true
categories: Art
tags: [C,Sort]
description:
published: true #default true
---

设数组为a[0…n-1]。

1. 初始时，a[0]自成1个有序区，无序区为a[1..n-1]。令i=1
2. 将a[i]并入当前的有序区a[0…i-1]中形成a[0…i]的有序区间。
3. i++并重复第二步直到i==n-1。排序完成。

```cpp
//ai[10] = {1,2,3...,8,9,10}; iStart=0, iEnd=10;
void vInsertionSort(int ai[], int iStart, int iEnd)
{
    int i = 0;
    int j = 0;
    int iTemp = 0;
    for (i=iStart+1; i<iEnd; i++)
    {
        for (j=i; j>iStart; j--)
        {
            if(ai[j] < ai[j-1])
            {
                iTemp = ai[j];
                ai[j] = ai[j-1];
                ai[j-1] = iTemp;
            }
        }
    }
}
```

### 补充

对数组中的数字 以特定步长排序。

比如：

1. 对于数组a[10],只对下标0,2,4,6,8的数字排序，步长则为2。
2. 对于数组a[10],只对下标0,3,6,9的数字排序，步长则为3。

```cpp
#include <iostream>
#include <stdio.h>
using namespace std;

void vPrint(int a[], int n)
{
    for (int i = 0; i < n; i++) {
        printf("%d ", a[i]);
    }
    printf("\n");
}

/*
 * @param a[]: 待排序的数组
 * @param n: 数组中数字的个数
 * @param gap: 步长
 * @param iStartIndex: 开始排序的下标
 */
void vInsertSortWithGap(int a[], int n, int gap, int iStartIndex)
{
    for (int i = iStartIndex; i < n; i += gap) {
        for (int j = i; j >= 0; j -= gap) {
            if((j-gap >= 0) && (a[j] < a[j-gap])) {
                swap(a[j], a[j-gap]);
            }
        }
    }
}

int main()
{
    int a[] = {5,1,3,4,9,7,6,10,2,8};
    vPrint(a, 10);
    vInsertSortWithGap(a, 10, 2, 0);
    vPrint(a, 10);
    return 0;
}
```

int a[] = {5,1,3,4,9,7,6,10,2,8};

vInsertSortWithGap(a, 10, 1, 0);

输出：1 2 3 4 5 6 7 8 9 10

<hr />

vInsertSortWithGap(a, 10, 2, 0);

输出：`2` 1 `3` 4 `5` 7 `6` 10 `9` 8

<hr />

vInsertSortWithGap(a, 10, 2, 1);

输出：5 `1` 3 `4` 9 `7` 6 `8` 2 `10`

<hr />

vInsertSortWithGap(a, 10, 3, 0);

输出：`4` 1 3 `5` 9 7 `6` 10 2 `8`

<hr />

看懂上面的程序之后，就可以学[希尔排序](http://mdgsf.github.io/ai/2016/04/08/ai-sort-shellSort.html)了。
