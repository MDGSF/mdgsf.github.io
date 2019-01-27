---
layout: post
title: "[Design Pattern] 工厂模式"
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
    virtual CSingleCore* CreateSingleCore() = 0;
};

class CFactoryA : public CFactory
{
public:
    CSingleCoreA* CreateSingleCore();
};

class CFactoryB : public CFactory
{
public:
    CSingleCoreB* CreateSingleCore();
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

CSingleCoreA* 
CFactoryA::CreateSingleCore()
{
    return new CSingleCoreA();
}

CSingleCoreB* 
CFactoryB::CreateSingleCore()
{
    return new CSingleCoreB();
}

```


### main.cpp

```
#include <stdio.h>
#include "CSingleCore.h"

int main()
{
    CFactoryA oFactoryA;
    CSingleCoreA * pSingleCoreA = oFactoryA.CreateSingleCore();
    pSingleCoreA->Show();
    delete pSingleCoreA;

    CFactoryB oFactoryB;
    CSingleCoreB * pSingleCoreB = oFactoryB.CreateSingleCore();
    pSingleCoreB->Show();
    delete pSingleCoreB;

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








