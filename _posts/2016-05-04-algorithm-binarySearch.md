---
layout: post
title:  "二分查找"
date:   2016-05-04
comments: true
categories: Art
tags: art, algorithm, binarySearch
description:
published: true
---

### Code

```c++
#include <stdio.h>

int rank(int key, int a[], int iLen)
{
    int lo = 0;
    int hi = iLen - 1;
    while (lo <= hi)
    {
        int mid = lo + (hi-lo)/2;
        if(key < a[mid]) hi = mid - 1;
        else if(key > a[mid]) lo = mid + 1;
        else return mid;
    }
    return -1;
}

int main()
{
    int a[] = {1, 3, 5, 7, 9, 12, 32, 55, 98, 100};
    int iLen = sizeof(a) / sizeof(int);
    printf("%d\n", rank(12, a, iLen));
    return 0;
}
```

### 时间复杂度分析

```
假设有 n 个元素，查找了 k 次才找到。

第 1 次查找:   n/2
第 2 次查找:   ( n/2 ) / 2 = n/4 = n/(2^2)
第 3 次查找:   ( n/(2^2) ) / 2 = n/(2^3)
第 4 次查找:   ( n/(2^3) ) / 2 = n/(2^4)
...
第 k 次查找:   n/(2^k)

n/(2^k) 代表的是还剩下的元素的个数
n/(2^k) >= 1
当 n/(2^k) == 1 时，查找结束。

所以令 n/(2^k) = 1 ， 即
==>   n = 2^k
==>   k = log(n) //以2为基

所以时间复杂度为 O( log(n) )
```
