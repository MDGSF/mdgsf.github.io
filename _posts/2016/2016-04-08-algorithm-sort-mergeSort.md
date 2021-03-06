---
layout: post
title: "[排序] 归并排序"
date: 2016-04-08
author: mdgsf
comments: true
categories: Art
tags: [C,Sort]
description:
published: true #default true
---

归并排序是建立在归并操作上的一种有效的排序算法。该算法是采用分治法（Divide and Conquer）的一个非常典型的应用。

首先考虑下如何将将二个有序数列合并。这个非常简单，只要从比较二个数列的第一个数，谁小就先取谁，取了后就在对应数列中删除这个数。然后再进行比较，如果有数列为空，那直接将另一个数列的数据依次取出即可。

```cpp
#include <iostream>
#include <stdio.h>
using namespace std;

void vPrint(int a[], int n)
{
    for (int i = 0; i < n; i++) {
        printf("%d ", a[i]);
    }
    putchar('\n');
}

//merge sorted array a and b into array c
void MergeArray(int a[], int n, int b[], int m, int c[])
{
    int i,j,k;
    i=j=k=0;
    while (i < n && j < m) {
        if(a[i] < b[j])
            c[k++] = a[i++];
        else
            c[k++] = b[j++];
    }

    while (i < n) c[k++] = a[i++];

    while (j < m) c[k++] = b[j++];
}

int main()
{
    int a[] = {1, 2, 3};
    int b[] = {5, 8, 9, 212};
    int c[100];
    MergeArray(a, 3,  b, 4,  c);
    vPrint(a, 7);
    return 0;
}
```

可以看出合并有序数列的效率是比较高的，可以达到O(n)。

解决了上面的合并有序数列问题，再来看归并排序，其的基本思路就是将数组分成二组A，B，如果这二组组内的数据都是有序的，那么就可以很方便的将这二组数据进行排序。如何让这二组组内数据有序了？

可以将A，B组各自再分成二组。依次类推，当分出来的小组只有一个数据时，可以认为这个小组组内已经达到了有序，然后再合并相邻的二个小组就可以了。这样通过先递**归**的分解数列，再合**并**数列就完成了**归并**排序。

```cpp
#include <iostream>
#include <stdio.h>
using namespace std;

void mergeArray(int a[], int first, int mid, int last, int temp[])
{
    int i = first, j = mid + 1;
    int m = mid,   n = last;
    int k = 0;

    while (i<=m && j<=n) {
        if(a[i] <= a[j]) temp[k++] = a[i++];
        else temp[k++] = a[j++];
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
    int a[] = {3,2,3421,1234234,1234,12,421,41,434,643,435,355};
    MergeSort(a, 12);
    for (int i = 0; i < 12; i++) {
        printf("%d ", a[i]);
    }
    printf("\n");
    return 0;
}
```

归并排序的效率是比较高的，设数列长为N，将数列分开成小数列一共要logN步，每步都是一个合并有序数列的过程，时间复杂度可以记为O(N)，故一共为O(N*logN)。因为归并排序每次都是在相邻的数据中进行操作，所以归并排序在O(N*logN)的几种排序方法（快速排序，归并排序，希尔排序，堆排序）也是效率比较高的。

### 链接

[白话经典算法系列之五 归并排序](http://blog.csdn.net/morewindows/article/details/6678165)
