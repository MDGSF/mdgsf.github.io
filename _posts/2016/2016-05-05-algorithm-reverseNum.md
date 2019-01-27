---
layout: post
title:  "[数组] 反转数组"
date:   2016-05-05
comments: true
categories: Art
tags: [Art,Algorithm,C]
description:
published: true
---

```c++
#include <stdio.h>

void reverseNum(int a[], int iLen)
{
    int N = iLen;
    for (int i = 0; i < N/2; i++)
    {
        int temp = a[i];
        a[i] = a[N-1-i];
        a[N-1-i] = temp;
    }
}

int main()
{
    int a[] = {1,2,3,4,5};
    int iLen = sizeof(a) / sizeof(int);
    reverseNum(a, iLen);

    for (int i = 0; i < iLen; i++)
    {
        printf("%d ", a[i]);
    }
    printf("\n");

    getchar();getchar();
    return 0;
}
```
