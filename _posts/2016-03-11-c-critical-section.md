---
layout: post
title:  "[windows] CRITICAL_SECTION 临界区"
date:   2016-03-11
comments: true
categories: windows
tags: CRITICAL_SECTION
description:
published: true
---


```cpp
CRITICAL_SECTION g_cs;
InitializeCriticalSectionAndSpinCount(&g_cs, 400);

EnterCriticalSection(&g_cs);
LeaveCriticalSection(&g_cs);

DeleteCriticalSection(&g_cs);
```

## 可以对同一个锁加锁两次。

```cpp
#include <windows.h>
#include <stdio.h>

CRITICAL_SECTION g_cs;

int main()
{
    InitializeCriticalSectionAndSpinCount(&g_cs, 400);

    printf("Before enter\n");
    EnterCriticalSection(&g_cs);
    printf("Enter 1\n");

    EnterCriticalSection(&g_cs);
    printf("Enter 2\n");

    LeaveCriticalSection(&g_cs);
    printf("Leave 1\n");

    LeaveCriticalSection(&g_cs);
    printf("Leave 2\n");

    DeleteCriticalSection(&g_cs);
    return 0;
}
```


