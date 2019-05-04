---
layout: post
title: "[基础] atoi"
date: 2016-12-07
author: mdgsf
comments: true
categories: Art
tags: [C]
description:
published: true #default true
---

<<编程之法>> 学习

```cpp
#include <stdio.h>
#include <ctype.h>
#include <string.h>

int StrToInt(const char * s)
{
    static const int MAX_INT = (int)((unsigned)~0>>1);
    static const int MIN_INT = -(int)((unsigned)~0>>1) - 1;

    if(NULL == s || strlen(s) <= 0)
    {
        return 0;
    }

    while (isspace(*s))
    {
        s++;
    }

    int iSign = 1;
    if(*s == '+' || *s == '-')
    {
        if(*s == '-')
        {
            iSign = -1;
        }
        s++;
    }

    unsigned int n = 0;
    while (isdigit(*s))
    {
        int c = *s - '0';
        if(iSign > 0 && (n > MAX_INT/10 || (n == MAX_INT/10 && c > MAX_INT % 10)))
        {
            return MAX_INT;
        }
        else if(iSign < 0 && (n > (unsigned)MAX_INT/10 || (n == (unsigned)MAX_INT/10 && c > (unsigned)MAX_INT % 10)))
        {
            return MIN_INT;
        }

        n = n * 10 + c;
        s++;
    }

    return (iSign > 0) ? n : -n;
}

void vTest(const char * pcCaseName, const char * s, int iExpectResult)
{
    int iRet = StrToInt(s);
    if(iRet == iExpectResult)
    {
        printf("%s: pass\n", pcCaseName);
    }
    else
    {
        printf("%s: failed\n", pcCaseName);
        printf("iRet = %d, iExpectResult = %d\n", iRet, iExpectResult);
    }
}

void vTest1()
{
    vTest("vTest1", "123", 123);
}

void vTest2()
{
    vTest("vTest2", "-123", -123);
}

void vTest3()
{
    vTest("vTest3", " 0123", 123);
}

void vTest4()
{
    vTest("vTest4", " -0123", -123);
}

void vTest5()
{
    vTest("vTest5", "  2147483647 ", 2147483647);
}

void vTest6()
{
    vTest("vTest6", "2147483648", 2147483647);
}

void vTest7()
{
    vTest("vTest7", "2147483646", 2147483646);
}

void vTest8()
{
    vTest("vTest8", "-2147483648", -2147483648);
}

void vTest9()
{
    vTest("vTest9", "-2147483649", -2147483648);
}

void vTest10()
{
    vTest("vTest10", "", 0);
}

void vTest11()
{
    vTest("vTest11", NULL, 0);
}

void vTest12()
{
    vTest("vTest12", "123abc", 123);
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
    vTest9();
    vTest10();
    vTest11();
    vTest12();
    return 0;
}
```
