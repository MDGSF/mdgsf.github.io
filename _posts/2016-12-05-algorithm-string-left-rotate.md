---
layout: post
title: "[字符串] 旋转"
date: 2016-12-05
author: mdgsf
comments: true
categories: Art
tags: c 
description:
published: true #default true
---

<<编程之法>> 学习

### Question

给定一个字符串，要求将字符串的前面若干个字符移动到字符串的尾部。例如：

将字符串"abcdef"的前面3个字符移动到尾部，那么原字符串变成"defabc"。


```cpp
#include <stdio.h>
#include <string.h>

void Reverse(char * pcStart, char * pcEnd)
{
    if(NULL == pcStart || NULL == pcEnd)
    {
        return ;
    }

    while (pcStart < pcEnd)
    {
        char c = *pcStart;
        *pcStart = *pcEnd;
        *pcEnd = c;

        pcStart++;
        pcEnd--;
    }
}

void LeftRotate(char * pcStr, int iNum)
{
    if(NULL == pcStr || iNum <= 0)
    {
        return;
    }

    int iStrLen = strlen(pcStr);
    iNum = iNum % iStrLen;

    Reverse(pcStr, pcStr + iNum - 1);
    Reverse(pcStr + iNum, pcStr + iStrLen - 1);
    Reverse(pcStr, pcStr + iStrLen - 1);
}

void vTest(const char * pcCaseName, char * pcStr, int iNum, const char * pcExpectResult)
{
    LeftRotate(pcStr, iNum);
    if(strcmp(pcExpectResult, pcStr) == 0)
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
    char ac[] = "abcdefghi";
    vTest("vTest1", ac, 3, "defghiabc");
}

void vTest2()
{
    char ac[] = "abcdefghi";
    vTest("vTest2", ac, 10, "bcdefghia");
}

int main()
{
    vTest1();
    vTest2();
    return 0;
}
```



