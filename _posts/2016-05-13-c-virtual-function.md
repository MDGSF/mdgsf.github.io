---
layout: post
title:  "[C/C++] 虚函数"
date:   2016-05-13
comments: true
categories: C/C++
tags: c, virtual
description:
published: true
---


```cpp
#include <iostream>
#include <stdio.h>
using namespace std;

class CVirtualBase
{
public:
    virtual void vPureVirtualFunc() = 0;
    virtual void vVirtualFunc() {
        printf("CVirtualBase::vVirtualFunc()\n");
    }
    void vFunc() {
        printf("CVirtualBase::vFunc()\n");
    }
};

class CSubVirtual : CVirtualBase
{
public:
    void vPureVirtualFunc() {
        printf("CSubVirtual::vPureVirtualFunc()\n");
    }

    void vVirtualFunc() {
        printf("CSubVirtual::vVirtualFunc()\n");
    }

    void vFunc() {
        printf("CSubVirtual::vFunc()\n");
    }
};

int main()
{
    //有virtual， p->fun() 取决于右边
    //没有virtual， p->fun() 取决于左边
    CVirtualBase * instance = (CVirtualBase *) new CSubVirtual();
    instance->vPureVirtualFunc(); //CSubVirtual::vPureVirtualFunc()
    instance->vVirtualFunc(); //CSubVirtual::vVirtualFunc()
    instance->vFunc(); //CVirtualBase::vFunc()

    getchar();getchar();
    return 0;
}
```
