---
layout: post
title: "[堆] topK--Maxheap"
date: 2016-12-09
author: mdgsf
comments: true
categories: Art
tags: [C]
description:
published: true #default true
---


### Question

在n个数中找出最小的k个数.

```cpp
#include <stdio.h>

void vSwap(int & a, int & b)
{
    int t = a;
    a = b;
    b = t;
}

void MaxHeap(int heap[], int i, int len)
{
    if(NULL == heap || len <= 0)
    {
        return ;
    }

    int iLargeIndex = -1;
    int iLeft = i * 2;
    int iRight = i * 2 + 1;

    if(iLeft <= len && heap[iLeft] > heap[i])
    {
        iLargeIndex = iLeft;
    }
    else
    {
        iLargeIndex = i;
    }

    if(iRight <= len && heap[iRight] > heap[iLargeIndex])
    {
        iLargeIndex = iRight;
    }

    if(iLargeIndex != i)
    {
        vSwap(heap[i], heap[iLargeIndex]);
        MaxHeap(heap, iLargeIndex, len);
    }
}

void BuildHeap(int heap[], int len)
{
    if(NULL == heap || len <= 0)
    {
        return ;
    }

    int iIndex = len / 2;
    for (int i = iIndex; i>=1; i--)
    {
        MaxHeap(heap, i, len);
    }
}


int main()
{
    int n = 7;
    int k = 3;
    int ai[] = {0, 10, 12, 3, 4, 5, 6, 7};
    int iSize = 3;
    BuildHeap(ai, iSize);

    int i;
    for (i = 4; i <= 7; i++)
    {
        if(ai[i] < ai[1])
        {
            vSwap(ai[i], ai[1]);
            MaxHeap(ai, 1, k);
        }
    }

    for (i = 0; i <= 7; i++)
    {
        printf("%d ", ai[i]);
    }
    printf("\n");

    return 0;
}
```


