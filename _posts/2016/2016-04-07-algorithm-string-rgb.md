---
layout: post
title: "[字符串] 调整为RGB的顺序"
date: 2016-04-07
author: mdgsf
comments: true
categories: Art
tags: [C,String]
description:
published: true #default true
---

题目：一个字符串只有‘R’、‘G’、‘B’组成，如何让所有的‘R’出现在前面，所有的‘G’在中间，所有的‘B’在最后。

要求：要求空间复杂度为O(1),只许遍历一遍字符串数组

思路：维护三个游标 i、j、k

i 指向开始， j 指向尾部，用于分别插入 R 、B

k 用于遍历，当发现是R时，与前面的 i 指的对象交换，i 后移；

当发现是B时，与后面的 j指的对象交换，j前移

当 k与j相遇后停止

## Code

```cpp
#include <iostream>
#include <stdio.h>
#include <string.h>
using namespace std;

void f(char * pcRgb)
{
    int iLen = strlen(pcRgb);
    int i = 0;
    int j = iLen - 1;
    int k = 0;
    while (k <= j)
    {
        if(pcRgb[k] == 'R')
        {
            char tmp = pcRgb[i]; pcRgb[i] = pcRgb[k]; pcRgb[k] = tmp;
            i++;
        }
        else if(pcRgb[k] == 'B')
        {
            char tmp = pcRgb[j]; pcRgb[j] = pcRgb[k]; pcRgb[k] = tmp;
            j--;
        }
        else
        {
            k++;
        }
    }
}

int main()
{
    char rgb[] = "BRBGGRBGRBBGRBGBRBGRBG";
    cout << rgb << endl;
    f(rgb);
    cout << rgb << endl;
    return 0;
}
```
