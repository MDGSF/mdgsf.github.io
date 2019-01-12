---
layout: post
title:  "[C++] 计算程序运行时间"
date:   2016-04-08
comments: true
categories: Art
tags: art, algorithm, c, time
description:
published: true
---


```cpp
#include <iostream>
#include <stdio.h>
#include <time.h>
#include <sys/timeb.h>
using namespace std;

void print_time(const char * head)
{
    struct timeb rawtime;
    ftime(&rawtime);

    struct tm * timeinfo = localtime(&rawtime.time);

    static int ms = rawtime.millitm;
    static unsigned long s = rawtime.time;
    int out_ms = rawtime.millitm - ms;
    unsigned long out_s = rawtime.time - s;
    ms = rawtime.millitm;
    s = rawtime.time;

    if(out_ms < 0)
    {
        out_ms += 1000;
        s -= 1;
    }
    printf("%s date/time is: %s \tused time is %lu s %d ms.\n", head, asctime(timeinfo), out_s, out_ms);
}

int main()
{
    print_time("head");
    for (int i = 0; i < 10000000; i++)
        ;
    print_time("tail");
    return 0;
}
```
