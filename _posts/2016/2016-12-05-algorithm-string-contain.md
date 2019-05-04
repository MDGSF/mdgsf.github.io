---
layout: post
title: "[字符串] 字符包含"
date: 2016-12-05
author: mdgsf
comments: true
categories: Art
tags: [C]
description:
published: true #default true
---

<<编程之法>> 学习

### 字符串的包含

```cpp
#include <stdio.h>
#include <string.h>

bool StringContain(const char * a, const char * b)
{
    if(NULL == a || NULL == b)
    {
        return false;
    }

    int aiExists[26] = {0};
    int iASize = strlen(a);
    int i = 0;
    for (i = 0; i < iASize; i++)
    {
        aiExists[ a[i] - 'a' ] = 1;
    }

    int iBSize = strlen(b);
    for (i = 0; i < iBSize; i++)
    {
        if(aiExists[ b[i] - 'a' ] != 1)
        {
            return false;
        }
    }
    return true;
}

void vTest(const char * pcCaseName, const char * a, const char * b, bool bExpectResult)
{
    bool bRet = StringContain(a, b);
    if(bRet == bExpectResult)
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
    vTest("vTest1", "abcd", "ac", true);
}

void vTest2()
{
    vTest("vTest2", "abcd", "ae", false);
}

int main()
{
    vTest1();
    vTest2();
    return 0;
}
```

### 位操作的方法

```cpp
bool StringContain(const char * a, const char * b)
{
    if(NULL == a || NULL == b)
    {
        return false;
    }

    int hash = 0;
    int iASize = strlen(a);
    int i = 0;
    for (i = 0; i < iASize; i++)
    {
        hash |= (1 << (a[i] - 'a'));
    }

    int iBSize = strlen(b);
    for (i = 0; i < iBSize; i++)
    {
        if((hash & (1 << (b[i] - 'a'))) == 0)
        {
            return false;
        }
    }
    return true;
}
```
