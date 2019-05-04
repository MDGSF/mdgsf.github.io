---
layout: post
title:  "[google] 使用breakpad生成dump文件"
date:   2017-09-13
comments: true
categories: google
tags: [Breakpad]
description:
published: true
---

## 什么是Google Breakpad？

Google Breakpad is a cross platform crash handler which generates minidumps when your application crash. Users can send these minidumps to you and it contains valuable information allowing you to figure out why it crashed on them.

## breakpad 的地址

<a href="https://github.com/google/breakpad" target="_blank">https://github.com/google/breakpad</a>

<a href="https://chromium.googlesource.com/breakpad/breakpad" target="_blank">https://chromium.googlesource.com/breakpad/breakpad</a>

获取源码：

```
git clone https://chromium.googlesource.com/breakpad/breakpad
```

## 安装python

这里面略过了，但是记住要使用Python2.x,，Python3.x会报错

## 什么是gyp

GYP（Generate Your Projects）是由 Chromium 团队开发的跨平台自动化项目构建工具，Chromium 便是通过 GYP 进行项目构建管理。

<a href="https://chromium.googlesource.com/external/gyp" target="_blank">https://chromium.googlesource.com/external/gyp</a>

```
git clone https://chromium.googlesource.com/external/gyp
```

**安装gyp**

```
cd gyp
python setup.py install
```

**拷贝gyp文件夹到breakpad\src\tools文件夹下**

## 使用gyp生成breakpad的sln工程文件

```
E:\git\breakpad\src\tools\gyp>set GYP_MSVS_VERSION=2015
E:\git\breakpad\src\tools\gyp>gyp.bat --no-circular-check "../../client/windows/breakpad_client.gyp"
Warning: Missing input files:
..\..\client\windows\unittests\..\..\..\testing\googlemock\src\gmock-all.cc
..\..\client\windows\unittests\..\..\..\testing\googletest\src\gtest-all.cc
..\..\client\windows\unittests\..\..\..\testing\googletest\src\gtest_main.cc
```

然后会在目录E:\git\breakpad\src\client\windows下生成breakpad_client.sln工程。

<img src="{{ site.url }}/images/2017/09/1301.png" alt="2017091301" />

## sln工程

<img src="{{ site.url }}/images/2017/09/1302.png" alt="2017091302" />

<img src="{{ site.url }}/images/2017/09/1303.png" alt="2017091303" />

<img src="{{ site.url }}/images/2017/09/1304.png" alt="2017091304" />

<img src="{{ site.url }}/images/2017/09/1305.png" alt="2017091305" />

## 生成的lib

<img src="{{ site.url }}/images/2017/09/1306.png" alt="2017091306" />

## 测试工程

```c++
#include <stdio.h>
#include "client/windows/handler/exception_handler.h"

using namespace google_breakpad;

static bool s_bMinidumpCallback(
    const wchar_t* dump_path,
    const wchar_t* minidump_id,
    void* context,
    EXCEPTION_POINTERS* exinfo,
    MDRawAssertionInfo* assertion,
    bool succeeded)
{
    if (succeeded)
    {
        printf("dump guid is %ws\n", minidump_id);
    }
    else
    {
        printf("dump failed\n");
    }
    fflush(stdout);
    return succeeded;
}

void errFun(int * p)
{
    *p = 1;
}

int main()
{
    google_breakpad::ExceptionHandler oExceptionHandler(
        L".",
        NULL,
        s_bMinidumpCallback,
        NULL,
        google_breakpad::ExceptionHandler::HANDLER_ALL
    );

    int * p = NULL;
    errFun(p);
    return 0;
}
```

运行测试代码会生成一个dump文件

<img src="{{ site.url }}/images/2017/09/1307.png" alt="2017091307" />

把dump文件复制到exe和pdb的同一个目录下，然后把dump文件拖动到vs2015中去，点击右上角的 **使用仅限本机进行调试**。

<img src="{{ site.url }}/images/2017/09/1308.png" alt="2017091308" />

## 参考链接

<a href="http://blog.csdn.net/wangshubo1989/article/details/53334033" target="_blank">http://blog.csdn.net/wangshubo1989/article/details/53334033</a>
