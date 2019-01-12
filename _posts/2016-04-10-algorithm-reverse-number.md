---
layout: post
title:  "从归并排序到数列的逆序数对"
date:   2016-04-10
comments: true
categories: Art
tags: c
description:
published: true
---

### 问题

在一个排列中，如果一对数的前后位置与大小顺序相反，即前面的数大于后面的数，那么它们就称为一个逆序数对。一个排列中逆序的总数就称为这个排列的逆序数。如{2，4，3，1}中，2和1，4和3，4和1，3和1是逆序数对，因此整个数组的逆序数对个数为4，现在给定一数组，要求统计出该数组的逆序数对个数。

计算数列的逆序数对个数最简单的方便就最从前向后依次统计每个数字与它后面的数字是否能组成逆序数对。代码如下：

```cpp
#include <stdio.h>

int main()
{
    const int MAXN = 8;
    int a[MAXN] = {1, 7, 2, 9, 6, 4, 5, 3};
    int iCount = 0;

    for (int i = 0; i < MAXN; i++) {
        for (int j = i + 1; j < MAXN; j++) {
            if (a[i] > a[j])
                iCount++;
        }
    }

    printf("iCount=%d\n", iCount);
    return 0;
}
```

这种方法用到了双循环，时间复杂度为O(N^2)，是一个不太优雅的方法。因此我们尝试用其它方法来解决。


在《[归并排序](http://mdgsf.github.io/ai/2016/04/08/sort-mergeSort.html)》中观察归并排序——合并数列(1，3，5)与(2，4)的时候：

1．先取出前面数列中的1。

2．然后取出后面数列中的2，明显！这个2和前面的3，5都可以组成逆序数对即3和2，5和2都是逆序数对。

3．然后取出前面数列中的3。

4．然后取出后面数列中的4，同理，可知这个4和前面数列中的5可以组成一个逆序数对。

这样就完成了逆序数对的统计，归并排序的时间复杂度是O(N * LogN)，因此这种从归并排序到数列的逆序数对的解法的时间复杂度同样是O(N * LogN)，下面给出代码：

```cpp
#include <iostream>
#include <stdio.h>
using namespace std;

int g_iCount;

void mergeArray(int a[], int first, int mid, int last, int temp[])
{
    int i = first, j = mid + 1;
    int m = mid,   n = last;
    int k = 0;

    while (i<=m && j<=n) {
        if(a[i] <= a[j])
            temp[k++] = a[i++];
        else {
            temp[k++] = a[j++];
            g_iCount += m - i + 1;
        }
    }

    while (i<=m) temp[k++] = a[i++];

    while (j<=n) temp[k++] = a[j++];

    for (i = 0; i < k; i++)
        a[first+i] = temp[i];
}

void mergeSort(int a[], int first, int last, int temp[])
{
    if(first < last)
    {
        int mid = (first+last)/2;
        mergeSort(a, first, mid, temp);
        mergeSort(a, mid+1, last, temp);
        mergeArray(a, first, mid, last, temp);
    }
}

bool MergeSort(int a[], int n)
{
    int * pi = new int[n];
    if(NULL == pi) {
        return false;
    }

    mergeSort(a, 0, n-1, pi);

    delete[] pi;
    return true;
}

int main()
{
    int a[] = {1, 7, 2, 9, 6, 4, 5, 3};
    MergeSort(a, 8);
    printf("g_iCount=%d\n", g_iCount);
    return 0;
}
```


### 链接

[白话经典算法系列之九 从归并排序到数列的逆序数对](http://blog.csdn.net/morewindows/article/details/8029996)
