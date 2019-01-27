---
layout: post
title: "[排序] 快速排序"
date: 2016-04-08
author: mdgsf
comments: true
categories: Art
tags: [C,Sort]
description:
published: true #default true
---


强烈推荐<<算法导论>>，讲的很清楚。

```cpp
#include <stdio.h>
#include "quicksort.h"

void vSwap(int & iA, int & iB)
{
    int iT = iA;
    iA = iB;
    iB = iT;
}

int iPartition(int a[], int iStart, int iEnd)
{
    int iX = a[iEnd];
    int iIndex = iStart-1;
    for (int i = iStart; i<iEnd; i++)
    {
        if( a[i] <= iX )
        {
            iIndex++;
            vSwap(a[iIndex], a[i]);
        }
    }
    vSwap(a[iIndex+1], a[iEnd]);
    return iIndex+1;
}

void vQuickSort(int a[], int iStart, int iEnd)
{
    if(iStart >= iEnd)
    {
        return ;
    }

    int iQ = iPartition(a, iStart, iEnd);
    vQuickSort(a, iStart, iQ-1);
    vQuickSort(a, iQ+1, iEnd);
}
```
