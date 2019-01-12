---
layout: post
title: "[排序] 冒泡排序"
date: 2016-04-07
author: mdgsf
comments: true
categories: Art
tags: c, sort
description:
published: true #default true
---


冒泡排序是非常容易理解和实现，，以从小到大排序举例：

设数组长度为N。

1. 比较相邻的前后二个数据，如果前面数据大于后面的数据，就将二个数据交换。

2. 这样对数组的第0个数据到N-1个数据进行一次遍历后，最大的一个数据就“沉”到数组第N-1个位置。

3. N=N-1，如果N不为0就重复前面二步，否则排序完成。



```cpp
#include <stdio.h>

void vPrint(int a[], int n)
{
    for (int i = 0; i < n; i++)
        printf("%d ", a[i]);
    printf("\n");
}

void vSwap(int & a, int & b)
{
    int tmp = a;
    a = b;
    b = tmp;
}

void vBubbleSort(int a[], int n)
{
    int j;
    for (int i = 0; i < n-1; i++) {
        for (j = 1; j < n - i; j++) {
            if(a[j-1] > a[j]) {
                vSwap(a[j-1], a[j]);
            }
        }
    }
}

int main()
{
    int a[] = {3, 456, 784, 3421, 97832,164, 21, 883, 3, 21351};
    vPrint(a, 10);
    vBubbleSort(a, 10);
    vPrint(a, 10);
    return 0;
}
```

### 时间复杂度分析

```
外层循环了 n-1 次，也就是从第 0 个元素到第 n-1 个元素都要遍历。

第 1 个元素:    需要比较 n-1 次
第 2 个元素:    需要比较 n-2 次
第 3 个元素:    需要比较 n-3 次
第 4 个元素:    需要比较 n-4 次
...
第 n-1 个元素:    需要比较 1 次

所以一共需要比较的次数为：
(n-1) + (n-2) + (n-3) + ... + 1
=>  (n-1)[(n-1) + 1] / 2
=>  (n^2)/2 - n/2

所以时间复杂度为 O( n^2 )
```



### 第二种

这个和前面的类似，就只是把小的数字向前冒泡，把最小的冒到下标0，然后下标1，一直到n-1。


### Code

```cpp
#include <stdio.h>
void vSwap(Item & iA, Item & iB)
{
    Item iT = iA;
    iA = iB;
    iB = iT;
}

/*
 *  int ai[10], iStart=0, iEnd=10
 */
void vBubble(Item a[], int iStart, int iEnd)
{
    int i = 0;
    int j = 0;
    for (i=iStart; i<iEnd; i++)
    {
        for (j = iEnd-1; j > i; j--)
        {
            if(a[j] < a[j-1])
            {
                vSwap(a[j], a[j-1]);
            }
        }
    }
}
```

### 优化1

下面对其进行优化，设置一个标志，如果这一趟发生了交换，则为true，否则为false。
如果有一趟没有发生交换，说明排序已经完成。

```cpp
#include <stdio.h>

void vPrint(int a[], int n)
{
    for (int i = 0; i < n; i++)
        printf("%d ", a[i]);
    printf("\n");
}

void vSwap(int & a, int & b)
{
    int tmp = a;
    a = b;
    b = tmp;
}

void vBubbleSort(int a[], int n)
{
    int j = 0;
    bool bHasSwap = true;
    for (int i = 0; i < n-1 && bHasSwap; i++) {
        bHasSwap = false;
        for (j = 1; j < n - i; j++) {
            if(a[j-1] > a[j]) {
                vSwap(a[j-1], a[j]);
                bHasSwap = true;
            }
        }
    }
}

int main()
{
    int a[] = {3, 456, 784, 3421, 97832,164, 21, 883, 3, 21351};
    vPrint(a, 10);
    vBubbleSort(a, 10);
    vPrint(a, 10);
    return 0;
}
```


### 优化2

再做进一步的优化。如果有100个数的数组，仅前面10个无序，后面90个都已排好序且都大于前面10个数字，那么在第一趟遍历后，最后发生交换的位置必定小于10，且这个位置之后的数据必定已经有序了，记录下这位置，第二次只要从数组头部遍历到这个位置就可以了。

```cpp
#include <stdio.h>

void vPrint(int a[], int n)
{
    for (int i = 0; i < n; i++)
        printf("%d ", a[i]);
    printf("\n");
}

void vSwap(int & a, int & b)
{
    int tmp = a;
    a = b;
    b = tmp;
}

void vBubbleSort(int a[], int n)
{
    int j = 0;
    int k = 0;
    for (int iFlag = n; iFlag > 0; ) {
        k = iFlag;
        iFlag = 0;
        for (j = 1; j < k; j++) {
            if(a[j-1] > a[j]) {
                vSwap(a[j-1], a[j]);
                iFlag = j;
            }
        }
    }
}

int main()
{
    int a[] = {3, 456, 784, 3421, 97832,164, 21, 883, 3, 21351};
    vPrint(a, 10);
    vBubbleSort(a, 10);
    vPrint(a, 10);
    return 0;
}
```

### 总结

优化1 和 优化2 都只是对于有序的情况做优化，完全随机的数组，效率并没有提高。

### 链接

[白话经典算法系列之一 冒泡排序](http://blog.csdn.net/morewindows/article/details/6657829)
