---
layout: post
title:  "[Windows] 输出GetLastError详细信息"
date:   2016-12-01
comments: true
categories: Windows
tags: [Windows]
description:
published: true
---

```cpp
#include <Windows.h>
#include <strsafe.h>

void ErrorExit()
{
    void * lpMsgBuf;
    FormatMessageA(
        FORMAT_MESSAGE_ALLOCATE_BUFFER |
        FORMAT_MESSAGE_FROM_SYSTEM |
        FORMAT_MESSAGE_IGNORE_INSERTS,
        NULL,
        GetLastError(),
        MAKELANGID(LANG_NEUTRAL, SUBLANG_DEFAULT),
        (char *)&lpMsgBuf,
        0,
        NULL);

    printf("**************************** error = %s\n", lpMsgBuf);

    LocalFree(lpMsgBuf);
}

int main()
{
    if( ! GetProcessId(NULL) )
    {
        ErrorExit();
    }
    return 0;
}
```
