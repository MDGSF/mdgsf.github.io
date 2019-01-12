---
layout: post
title:  "[lwip] protect and unprotect"
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
#include <pthread.h>

#define LWIP_UNUSED_ARG(x) (void)x
typedef int sys_prot_t;

static pthread_mutex_t lwprot_mutex = PTHREAD_MUTEX_INITIALIZER;
static pthread_t lwprot_thread = (pthread_t)0xDEAD;
static int lwprot_count = 0;

sys_prot_t
sys_arch_protect(void)
{
    if(lwprot_thread != pthread_self())
    {
        pthread_mutex_lock(&lwprot_mutex);
        lwprot_thread = pthread_self();
        lwprot_count = 1;
    }
    else
    {
        lwprot_count++;
    }
    return 0;
}

void 
sys_arch_unprotect(sys_prot_t pval)
{
    LWIP_UNUSED_ARG(pval);
    if(lwprot_thread == pthread_self())
    {
        if(--lwprot_count == 0)
        {
            lwprot_thread = (pthread_t)0xDEAD;
            pthread_mutex_unlock(&lwprot_mutex);
        }
    }
}

int g_iGloNum = 0;

void * thread(void * pArg)
{
    for (int i = 0; i < 10; i++)
    {
        sys_arch_protect();
        printf("thread g_iGloNum=%d\n", ++g_iGloNum);
        sys_arch_unprotect(0);
        sleep(1);
    }
    return NULL;
}

int main()
{
    printf("hello world! g_iGloNum=%d\n", g_iGloNum);

    pthread_t iThreadID;
    pthread_create(&iThreadID, NULL, thread, NULL);

    for (int i = 0; i < 10; i++)
    {
        sys_arch_protect();
        printf("main g_iGloNum=%d\n", --g_iGloNum);
        sys_arch_unprotect(0);
        sleep(2);
    }

    pthread_join(iThreadID, NULL);
    return 0;
}
```

g++ main.cpp -pthread


