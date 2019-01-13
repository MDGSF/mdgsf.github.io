---
layout: post
title:  "[Windows] char和wchar 转换"
date:   2016-05-12
comments: true
categories: Windows
tags: windows
description:
published: true
---


```cpp
#include <stdio.h>
#include <windows.h>

#pragma comment(lib, "ws2_32.lib")

#define MAX_FILE_NAME_LEN 256

#define utf8(str) WcharToChar(L##str)
const char * WcharToChar(const wchar_t * pwcStr)
{
    static char acBuf[1024];
    WideCharToMultiByte(CP_UTF8, 0, pwcStr, -1, acBuf, sizeof(acBuf), NULL, NULL);
    return acBuf;
}
const wchar_t * CharToWchar(const char * pcStr)
{
    static wchar_t awcBuf[1024];
    MultiByteToWideChar(CP_UTF8, 0, pcStr, -1, awcBuf, sizeof(awcBuf));
    return awcBuf;
}

int main()
{
    char acPathBuf[MAX_FILE_NAME_LEN] = "F:\\share\\git\\mdgsf";
    printf("%s\n", acPathBuf);

    /** convert from local to unicode **/
    DWORD dErrCode = 0;
    wchar_t acUnicode[MAX_FILE_NAME_LEN] = {0};
    if(0 == MultiByteToWideChar(CP_ACP, 0, (LPCSTR)acPathBuf, -1, (LPWSTR)acUnicode, MAX_FILE_NAME_LEN))
    {
        dErrCode = GetLastError();
        printf("convert to unicode fail, error code: %d\n", dErrCode);
        return -1;
    }

    /** convert from unicode to utf-8 **/
    char acUTF8[MAX_FILE_NAME_LEN] = {0};
    if(0 == WideCharToMultiByte(CP_UTF8, 0, (LPCWSTR)acUnicode, -1, (LPSTR)acUTF8, MAX_FILE_NAME_LEN, NULL, NULL))
    {
        dErrCode = GetLastError();
        printf("convert to utf-8 fail, error code: %d\n", dErrCode);
        return -1;
    }
    printf("%s\n", acUTF8);

    getchar();getchar();
    return 0;
}
```
