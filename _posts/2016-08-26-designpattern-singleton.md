---
layout: post
title: "[Design Pattern] Singleton单例模式"
date: 2016-08-26
author: mdgsf
comments: true
categories: DesignPattern
tags: [Singleton]
description:
published: true #default true
---


### CLocker.h

```
#ifndef CLOCKER_H
#define CLOCKER_H

#include <pthread.h>

class CLocker
{
public:
    CLocker() { pthread_mutex_init(&mutex, NULL); }
    ~CLocker() { pthread_mutex_destroy(&mutex); }
    void lock() { pthread_mutex_lock(&mutex); }
    void unlock() { pthread_mutex_unlock(&mutex); }
private:
    pthread_mutex_t mutex;
};

#endif
```


## 懒汉模式


### CLazySingleton.h

```
#ifndef CLAZY_SINGLETON_H
#define CLAZY_SINGLETON_H

#include <stdio.h>
#include "CLocker.h"

class CLazySingleton
{
public:
    static CLazySingleton * getInstance();
private:
    CLazySingleton();
    ~CLazySingleton();
    static CLazySingleton * m_pInstance;
    static CLocker m_lock;

    //use class CGarbo to delete m_pInstance
    class CGarbo
    {
    public:
        ~CGarbo() {
            printf("CLazySingleton ~CGarbo()\n");
            if(CLazySingleton::m_pInstance) {
                delete CLazySingleton::m_pInstance;
            }
        }
    };
    static CGarbo m_garbo;
};

#endif
```


### CLazySingleton.cpp

```
#include "CLazySingleton.h"

CLazySingleton::CGarbo CLazySingleton::m_garbo;
CLazySingleton * CLazySingleton::m_pInstance = NULL;
CLocker CLazySingleton::m_lock;

CLazySingleton * CLazySingleton::getInstance()
{
    if(NULL == m_pInstance)
    {
        m_lock.lock();
        if(NULL == m_pInstance)
        {
            m_pInstance = new CLazySingleton();
        }
        m_lock.unlock();
    }
    return m_pInstance;
}

CLazySingleton::CLazySingleton()
{
    printf("CLazySingleton::CLazySingleton()\n");
}

CLazySingleton::~CLazySingleton()
{
    printf("CLazySingleton::~CLazySingleton()\n");
}
```


## 饿汉模式

### CHungrySingleton.h

```
#ifndef CHUNGRY_SINGLETON_H
#define CHUNGRY_SINGLETON_H

class CHungrySingleton
{
public:
    static const CHungrySingleton * getInstance();
private:
    CHungrySingleton();
    ~CHungrySingleton();
    static const CHungrySingleton * m_pInstance;
};

#endif
```


### CHungrySingleton.cpp

```
#include "CHungrySingleton.h"
#include <stdio.h>

const CHungrySingleton * CHungrySingleton::m_pInstance = new CHungrySingleton();

const CHungrySingleton * CHungrySingleton::getInstance()
{
    return m_pInstance;
}

CHungrySingleton::CHungrySingleton()
{
    printf("CHungrySingleton::CHungrySingleton()\n");
}

CHungrySingleton::~CHungrySingleton()
{
    printf("CHungrySingleton::~CHungrySingleton()\n");
}
```



### main.cpp

```
#include <stdio.h>
#include "CLazySingleton.h"
#include "CHungrySingleton.h"

int main()
{
    printf("main Singleton\n");

    CLazySingleton * pLazy1 =  CLazySingleton::getInstance();
    CLazySingleton * pLazy2 =  CLazySingleton::getInstance();
    if(pLazy1 == pLazy1) {
        printf("Lazy the same\n");
    }

    const CHungrySingleton * pHungry1 =  CHungrySingleton::getInstance();
    const CHungrySingleton * pHungry2 =  CHungrySingleton::getInstance();
    if(pHungry1 == pHungry2) {
        printf("Hungry the same\n");
    }
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
      CLazySingleton.o \
      CHungrySingleton.o

%.o : %.cpp
    $(CXX) -c $(CFLAGS) -fno-strict-aliasing $< -o $@


$(TARGET): $(OBJS)
    $(CXX) $(CFLAGS) -o $@ $(OBJS) 

clean:
    rm -rf *.o $(TARGET)
```





