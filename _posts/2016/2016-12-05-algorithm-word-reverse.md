---
layout: post
title: "[字符串] 单词翻转"
date: 2016-12-05
author: mdgsf
comments: true
categories: Art
tags: [C]
description:
published: true #default true
---

### Question

输入 "I am a student"

输出 "student a am I"

思路:

先把每个单词的翻转，然后再将整个字符串翻转。

即: "I am a student" --> "I ma a tneduts" --> "student a am I"

```cpp
#include <stdio.h>
#include <string.h>

void ReverseString(char * s, int from, int to)
{
    while (from < to)
    {
        char t = s[from];
        s[from++] = s[to];
        s[to--] = t;
    }
}

void WordReverse(char * pcSentence)
{
    int iStart = 0;
    int iEnd = 0;
    while (1)
    {
        if(pcSentence[iEnd] == '\0')
        {
            if(iStart != iEnd)
            {
                ReverseString(pcSentence, iStart, iEnd - 1);
            }
            break;
        }
        else if(pcSentence[iEnd] == ' ')
        {
            ReverseString(pcSentence, iStart, iEnd - 1);
            iStart = iEnd + 1;
            iEnd = iStart;
        }
        else
        {
            iEnd++;
        }
    }

    ReverseString(pcSentence, 0, strlen(pcSentence) - 1);
}

int main()
{
    char ac[] = "  I am a student.";
    WordReverse(ac);
    printf("ac = %s\n", ac);
    return 0;
}
```
