---
layout: post
title:  "A Simple C++ template"
date:   2016-03-11
comments: true
categories: Art
tags: [Art,Algorithm,C,Template]
description: "a simple template for c"
published: true
---

```cpp
#include <stdio.h>

//#define FILE
//#define DEBUG

#ifdef DEBUG
#define LOG_PRINTF(format, ...) \
    printf("<"__FILE__">(%05d): "format"", __LINE__, ##__VA_ARGS__)
#else
#define LOG_PRINTF(format, ...)
#endif

int main()
{
#ifdef FILE
    freopen("data.in", "r", stdin);
    freopen("data.out", "w", stdout);
#endif

    LOG_PRINTF("Hello world!\n");

    return 0;
}
```
