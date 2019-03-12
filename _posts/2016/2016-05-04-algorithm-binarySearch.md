---
layout: post
title:  "二分查找"
date:   2016-05-04
comments: true
categories: Art
tags: [Art,Algorithm,Binarysearch]
description:
published: true
---

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

### 代码 1

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

### 代码 2

```cpp
#include <stdio.h>

int iBinarySearch(int aiNum[], int iNumSize, int iVal)
{
    if(NULL == aiNum || iNumSize <= 0)
    {
        return -1;
    }

    int iStart = 0;
    int iEnd = iNumSize - 1;

    while (iStart <= iEnd)
    {
        int iMid = iStart + ((iEnd - iStart) >> 1);
        if(aiNum[iMid] < iVal)
        {
            iStart = iMid + 1;
        }
        else if(aiNum[iMid] > iVal)
        {
            iEnd = iMid - 1;
        }
        else
        {
            return iMid;
        }
    }

    return -1;
}

void vTest(const char * pcCaseName, int aiNum[], int iNumSize, int iVal, int iExpectVal)
{
    int iRet = iBinarySearch(aiNum, iNumSize, iVal);
    if(iRet == iExpectVal)
    {
        printf("%s: pass\n", pcCaseName);
    }
    else
    {
        printf("%s: failed\n", pcCaseName);
    }
}

void vTest1()
{
    int ai[] = {1, 2, 3, 4, 5, 6, 7};
    int iSize = sizeof ai / sizeof ai[0];
    int iVal = 4;
    vTest("vTest1", ai, iSize, iVal, 3);
}

void vTest2()
{
    int ai[] = {1, 2, 3, 4, 5, 6, 7};
    int iSize = sizeof ai / sizeof ai[0];
    int iVal = 8;
    vTest("vTest2", ai, iSize, iVal, -1);
}

void vTest3()
{
    int ai[] = {1, 2, 3, 4, 5, 6, 7};
    int iSize = sizeof ai / sizeof ai[0];
    int iVal = -1;
    vTest("vTest3", ai, iSize, iVal, -1);
}

void vTest4()
{
    int ai[] = {1, 2, 3, 4, 5, 6, 7};
    int iSize = sizeof ai / sizeof ai[0];
    int iVal = 1;
    vTest("vTest4", ai, iSize, iVal, 0);
}

void vTest5()
{
    int ai[] = {1, 2, 3, 4, 5, 6, 7};
    int iSize = sizeof ai / sizeof ai[0];
    int iVal = 7;
    vTest("vTest5", ai, iSize, iVal, 6);
}

void vTest6()
{
    int ai[] = {1, 2, 3, 4, 5, 6, 7};
    int iSize = sizeof ai / sizeof ai[0];
    int iVal = 6;
    vTest("vTest6", ai, iSize, iVal, 5);
}

void vTest7()
{
    int ai[] = {1};
    int iSize = sizeof ai / sizeof ai[0];
    int iVal = 1;
    vTest("vTest7", ai, iSize, iVal, 0);
}

void vTest8()
{
    int iVal = 1;
    vTest("vTest8", NULL, 0, iVal, -1);
}

int main()
{
    vTest1();
    vTest2();
    vTest3();
    vTest4();
    vTest5();
    vTest6();
    vTest7();
    vTest8();
    return 0;
}
```

