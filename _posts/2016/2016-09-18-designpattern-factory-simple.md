---
layout: post
title: "[Design Pattern] 简单工厂模式"
date: 2016-09-18
author: mdgsf
comments: true
categories: DesignPattern
tags: [Singleton]
description:
published: true #default true
---

### CSingleCore.h

```
#ifndef CSINGLE_CORE_H
#define CSINGLE_CORE_H

enum CTYPE
{
    COREA,
    COREB
};

class CSingleCore
{
public:
    CSingleCore();
    virtual ~CSingleCore();
    virtual void Show() = 0;
};

class CSingleCoreA : public CSingleCore
{
public:
    CSingleCoreA();
    virtual ~CSingleCoreA();
    void Show();
};

class CSingleCoreB : public CSingleCore
{
public:
    CSingleCoreB();
    virtual ~CSingleCoreB();
    void Show();
};

class CFactory
{
public:
    CSingleCore* CreateSingleCore(enum CTYPE type);
};

#endif
```

### CSingleCore.cpp

```
#include "CSingleCore.h"
#include <stdio.h>

CSingleCore::CSingleCore()
{
    printf("CSingleCore::CSingleCore()\n");
}
CSingleCore::~CSingleCore()
{
    printf("CSingleCore::~CSingleCore()\n");
}


CSingleCoreA::CSingleCoreA()
{
    printf("CSingleCoreA::CSingleCoreA()\n");
}
CSingleCoreA::~CSingleCoreA()
{
    printf("CSingleCoreA::~CSingleCoreA()\n");
}
void CSingleCoreA::Show()
{
    printf("CSingleCoreA\n");
}


CSingleCoreB::CSingleCoreB()
{
    printf("CSingleCoreB::CSingleCoreB()\n");
}
CSingleCoreB::~CSingleCoreB()
{
    printf("CSingleCoreB::~CSingleCoreB()\n");
}
void CSingleCoreB::Show()
{
    printf("CSingleCoreB\n");
}

CSingleCore*
CFactory::CreateSingleCore(enum CTYPE type)
{
    if(type == COREA)
        return new CSingleCoreA();
    else if(type == COREB)
        return new CSingleCoreB();
    else
        return NULL;
}
```

### main.cpp

```
#include <stdio.h>
#include "CSingleCore.h"

int main()
{
    CFactory oFactory;
    CSingleCore * pSingleCore =  oFactory.CreateSingleCore(COREA);
    pSingleCore->Show();
    delete pSingleCore;

    pSingleCore =  oFactory.CreateSingleCore(COREB);
    pSingleCore->Show();
    delete pSingleCore;

    return 0;
}
```

### Makefile

```
CXX=g++
CFLAGS=

TARGET=main

OBJS= \
      main.o \
      CSingleCore.o

%.o : %.cpp
    $(CXX) -c $(CFLAGS) -fno-strict-aliasing $< -o $@


$(TARGET): $(OBJS)
    $(CXX) $(CFLAGS) -o $@ $(OBJS)

clean:
    rm -rf *.o $(TARGET)
```
