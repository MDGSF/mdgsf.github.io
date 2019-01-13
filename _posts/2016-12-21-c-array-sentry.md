---
layout: post
title:  "[C/C++] 数组哨兵"
date:   2016-12-21
comments: true
categories: C/C++
tags: c 
description: ""
published: true
---


在pcFindNormal()函数中，每次循环比较了 i<iLen 和 pcStr[i] == c 。

而在pcFindSentry()函数中，每次循环只比较了pcStr[i] == c。



```cpp
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/time.h>
 
#define BUF_SIZE 100000000
 
char *pcFindNormal(char *pcStr, char c)
{
    int iLen = strlen(pcStr);
    for (int i=0; i<iLen; i++)
    {
        if(pcStr[i] == c)
        {
            return &pcStr[i];
        }
    }
    return NULL;
}
 
char *pcFindSentry(char *pcStr, char c)
{
    int iLen = strlen(pcStr);
    if(pcStr[iLen-1] == c)
    {
        return &pcStr[iLen-1];
    }
 
    char cHold = pcStr[iLen-1];
    pcStr[iLen-1] = c;
    int i;
    for (i=0; ; i++)
    {
        if(pcStr[i] == c)
        {
            break;
        }
    }
    pcStr[iLen-1] = cHold;
 
    return (i<iLen-1) ? (&pcStr[i]) : NULL;
}

int main()
{
    char *pcStr = (char*)malloc(sizeof(char)*BUF_SIZE);
    if(pcStr == NULL)
    {
        printf("malloc errorn");
    }
 
    for (int i=0; i<BUF_SIZE; i++)
    {
        pcStr[i] = '2';
    }
    pcStr[BUF_SIZE-3] = '1';
 
    struct timeval stStart;
    struct timeval stEnd;
    struct timezone stZone;
 
    gettimeofday(&stStart, &stZone);
    char *pcRet = pcFindNormal(pcStr, '1');
    if(pcRet == NULL)
    {
        printf("pcFindNormal errorn");
    }
    gettimeofday(&stEnd, &stZone);
    printf("normal spend time: %ldmsn",
            (stEnd.tv_sec-stStart.tv_sec)*1000+(stEnd.tv_usec-stStart.tv_usec)/1000);
 
    memset(&stStart, 0, sizeof(stStart));
    memset(&stEnd, 0, sizeof(stEnd));
    memset(&stZone, 0, sizeof(stZone));
 
    gettimeofday(&stStart, &stZone);
    pcRet = pcFindSentry(pcStr, '1');
    if(pcRet == NULL)
    {
        printf("pcFindSentry errorn");
    }
    gettimeofday(&stEnd, &stZone);
    printf("Sentry spend time: %ldmsn",
            (stEnd.tv_sec-stStart.tv_sec)*1000+(stEnd.tv_usec-stStart.tv_usec)/1000);
			
	free(pcStr);
    return 0;
}
```