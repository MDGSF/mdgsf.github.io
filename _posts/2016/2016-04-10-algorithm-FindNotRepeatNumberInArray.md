---
layout: post
title:  "[位运算] 数组中只出现 1 次的两个数字"
date:   2016-04-10
comments: true
categories: Art
tags: [C]
description:
published: true
---

首先来看题目要求：

在一个数组中除两个数字只出现1次外，其它数字都出现了2次， 要求尽快找出这两个数字。

考虑下这个题目的简化版——数组中除一个数字只出现1次外，其它数字都成对出现，要求尽快找出这个数字。这个题目在之前的《[位操作总结](http://mdgsf.github.io/c/2016/04/10/c-bitset.html)》中的“位操作趣味应用”中就已经给出解答了。根据异或运算的特点，直接异或一次就可以找出这个数字。

现在数组中有两个数字只出现1次，直接异或一次只能得到这两个数字的异或结果，但光从这个结果肯定无法得到这个两个数字。因此我们来分析下简化版中“异或”解法的关键点，这个关键点也相当明显——数组只能有一个数字出现1次。

设题目中这两个只出现1次的数字分别为A和B，如果能将A，B分开到二个数组中，那显然符合“异或”解法的关键点了。因此这个题目的关键点就是将A，B分开到二个数组中。由于A，B肯定是不相等的，因此在二进制上必定有一位是不同的。根据这一位是0还是1可以将A，B分开到A组和B组。而这个数组中其它数字要么就属于A组，要么就属于B组。再对A组和B组分别执行“异或”解法就可以得到A，B了。而要判断A，B在哪一位上不相同，只要根据A异或B的结果就可以知道了，这个结果在二进制上为1的位都说明A，B在这一位上是不相同的。

比如int a[] = {1, 1, 3, 5, 2, 2}

整个数组异或的结果为3^5即 0x0011 ^ 0x0101 = 0x0110

对0x0110，第1位（由低向高，从0开始）就是1。因此整个数组根据第1位是0还是1分成两组。

a[0] =1  0x00`0`1  第一组

a[1] =1  0x00`0`1  第一组

a[2] =3  0x00`1`1  第二组

a[3] =5  0x01`0`1  第一组

a[4] =2  0x00`1`0  第二组

a[5] =2  0x00`1`0  第二组

第一组有{1, 1, 5}，第二组有{3, 2, 3}，明显对这二组分别执行“异或”解法就可以得到5和3了。

分析至些，相信代码不难写出，下面给出完整的源代码：

```cpp
#include <stdio.h>

void FindTwoNumberInArray(int a[], int n, int *pN1, int *pN2)
{
    int i, iIndex, temp;

    temp = 0;
    for (i = 0; i < n; i++)
        temp ^= a[i];

    int iBitsPerInt = sizeof(int) * 8;
    for (iIndex = 0; iIndex < iBitsPerInt; iIndex++) {
        if(((temp >> iIndex) & 1) == 1)
            break;
    }

    *pN1 = 0;
    *pN2 = 0;
    for (i = 0; i < n; i++)
    {
        if(((a[i] >> iIndex) & 1) == 0)
            *pN1 ^= a[i];
        else
            *pN2 ^= a[i];
    }
}

void PrintArray(int a[], int n)
{
    for (int i = 0; i < n; i++)
        printf("%d ", a[i]);
    printf("\n");
}

int main()
{
    const int MAXN = 10;
    int a[MAXN] = {1, 2, 3, 4, 1, 2, 3, 4, 0, 5};
    PrintArray(a, MAXN);

    int iNum1, iNum2;
    FindTwoNumberInArray(a, MAXN, &iNum1, &iNum2);
    printf("%d %d\n", iNum1, iNum2);
    return 0;
}
```

### 链接

[白话经典算法系列之十二--数组中只出现1次的两个数字](http://blog.csdn.net/morewindows/article/details/8214003)
