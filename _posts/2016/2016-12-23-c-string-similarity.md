---
layout: post
title:  "[C/C++] 字符串相似度"
date:   2016-12-23
comments: true
categories: C/C++
tags: [C]
description: ""
published: true
---

```
以下叙述摘自编程之美一书：
许多程序会大量使用字符串。对于不同的字符串，我们希望能够有办法判断其相似程序。我们定义一套操作方法来把两个不相同的字符串变得相同，具体的操作方法为：
1.修改一个字符（如把“a”替换为“b”）;　　
2.增加一个字符（如把“abdd”变为“aebdd”）;
3.删除一个字符（如把“travelling”变为“traveling”）;
比如，对于“abcdefg”和“abcdef”两个字符串来说，我们认为可以通过增加/减少一个“g”的方式来达到目的。上面的两种方案，都仅需要一 次 。把这个操作所需要的次数定义为两个字符串的距离，而相似度等于“距离+1”的倒数。也就是说，“abcdefg”和“abcdef”的距离为1，相似度 为1/2=0.5。
给定任意两个字符串，你是否能写出一个算法来计算它们的相似度呢？
原文的分析与解法　　
　　 不难看出，两个字符串的距离肯定不超过它们的长度之和（我们可以通过删除操作把两个串都转化为空串）。虽然这个结论对结果没有帮助，但至少可以知道，任意两个字符串的距离都是有限的。我们还是就住集中考虑如何才能把这个问题转化成规模较小的同样的子问题。如果有两个串A=xabcdae和B=xfdfa，它们的第一个字符是 相同的，只要计算A[2,...,7]=abcdae和B[2,...,5]=fdfa的距离就可以了。但是如果两个串的第一个字符不相同，那么可以进行 如下的操作（lenA和lenB分别是A串和B串的长度）。
1.删除A串的第一个字符，然后计算A[2,...,lenA]和B[1,...,lenB]的距离。
2.删除B串的第一个字符，然后计算A[1,...,lenA]和B[2,...,lenB]的距离。
3.修改A串的第一个字符为B串的第一个字符，然后计算A[2,...,lenA]和B[2,...,lenB]的距离。
4.修改B串的第一个字符为A串的第一个字符，然后计算A[2,...,lenA]和B[2,...,lenB]的距离。
5.增加B串的第一个字符到A串的第一个字符之前，然后计算A[1,...,lenA]和B[2,...,lenB]的距离。
6.增加A串的第一个字符到B串的第一个字符之前，然后计算A[2,...,lenA]和B[1,...,lenB]的距离。
在这个题目中，我们并不在乎两个字符串变得相等之后的字符串是怎样的。所以，可以将上面的6个操作合并为：
1.一步操作之后，再将A[2,...,lenA]和B[1,...,lenB]变成相字符串。
2.一步操作之后，再将A[2,...,lenA]和B[2,...,lenB]变成相字符串。
3.一步操作之后，再将A[1,...,lenA]和B[2,...,lenB]变成相字符串。
通过以上1和6,2和5，3和4的结合操作，最后两个字符串每个对应的字符会相同，但是这三种操作产生的最终的两个字符串是不一样的。我们不知道通过上述的三种结合那种使用的操作次数是最少的。所以我们要比较操作次数来求得最小值。
```

```cpp
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int iMin(int a, int b, int c)
{
    int min = a < b ? a : b;
    if(c < min)
    {
        min = c;
    }
    return min;
}

int iDistance(
        const char * pcStrA, int iAStart, int iAEnd,
        const char * pcStrB, int iBStart, int iBEnd,
        int ** ppiResult)
{
    printf("iAStart=%d,iAEnd=%d, iBStart=%d, iBEnd=%d\n", iAStart, iAEnd, iBStart, iBEnd);
    if(iAStart > iAEnd && iBStart > iBEnd) {
        return 0;
    }
    if(iAStart > iAEnd) {
        return iBEnd - iBStart + 1;
    }
    if(iBStart > iBEnd) {
        return iAEnd - iAStart + 1;
    }

    if(ppiResult[iAStart][iBStart] != 0) {
        return ppiResult[iAStart][iBStart];
    }

    if(pcStrA[iAStart] == pcStrB[iBStart])
    {
        int iRet = iDistance(pcStrA, iAStart+1, iAEnd, pcStrB, iBStart+1, iBEnd, ppiResult);
        if(iRet != 0) ppiResult[iAStart+1][iBStart+1] = iRet;
        return iRet;
    }

    int a = iDistance(pcStrA, iAStart+1, iAEnd, pcStrB, iBStart+1, iBEnd, ppiResult);
    int b = iDistance(pcStrA, iAStart+1, iAEnd, pcStrB, iBStart, iBEnd, ppiResult);
    int c = iDistance(pcStrA, iAStart, iAEnd, pcStrB, iBStart+1, iBEnd, ppiResult);
    printf("[%d][%d] = %d, [%d][%d] = %d, [%d][%d] = %d\n",
            iAStart+1, iBStart+1, a,
            iAStart+1, iBStart, b,
            iAStart, iBStart+1, c);
    if(a != 0) ppiResult[iAStart+1][iBStart+1] = a;
    if(b != 0) ppiResult[iAStart+1][iBStart] = b;
    if(c != 0) ppiResult[iAStart][iBStart+1] = c;

    ppiResult[iAStart][iBStart] = iMin(a, b, c) + 1;

    return ppiResult[iAStart][iBStart];
}

double dSimilarity(const char * pcStrA, const char * pcStrB)
{
    int iLenA = strlen(pcStrA);
    int iLenB = strlen(pcStrB);

    int ** ppiResult = (int**)malloc(sizeof(int*) * (iLenA+1));
    for (int i = 0; i < iLenA+1; i++)
    {
        ppiResult[i] = (int*)malloc(sizeof(int) * (iLenB+1));
        memset(ppiResult[i], 0, sizeof(int) * (iLenB+1));
    }

    int iDis = iDistance(pcStrA, 0, iLenA - 1, pcStrB, 0, iLenB - 1, ppiResult);
    printf("iDis = %d\n", iDis);
    return 1.0/(iDis + 1);
}

int main()
{
    char acStrA[] = "abc";
    char acStrB[] = "aef";

    printf("similarity = %lf\n", dSimilarity(acStrA, acStrB));

    return 0;
}
```
