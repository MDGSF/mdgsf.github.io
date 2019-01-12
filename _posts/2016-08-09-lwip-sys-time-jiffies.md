---
layout: post
title:  "[lwip] sys_now and  sys_jiffies"
date:   2016-08-09
comments: true
categories: lwip
tags: [lwip]
description:
published: true
---



```
#include <stdio.h>
#include <stdlib.h>
#include <unistd.h>
#include <time.h>

typedef unsigned long u32_t;

u32_t
sys_now(void)
{
    struct timespec ts;
    clock_gettime(CLOCK_MONOTONIC, &ts);
    return ts.tv_sec * 1000L + ts.tv_nsec / 1000000L;
}

u32_t
sys_jiffies(void)
{
    struct timespec ts;
    clock_gettime(CLOCK_MONOTONIC, &ts);
    return ts.tv_sec * 1000000000L + ts.tv_nsec;
}

int main()
{
    printf("sys_now = %lu millisecond\n", sys_now());
    printf("sys_jiffies = %lu nanosecond\n", sys_jiffies());
    return 0;
}
```