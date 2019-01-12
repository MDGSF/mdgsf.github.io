---
layout: post
title:  "[pthread] join"
date:   2016-09-18
comments: true
categories: pthread
tags: pthread
description:
published: true
---


```
#include <stdio.h>
#include <stdlib.h>
#include <pthread.h>
#include <math.h>

#define NUM_THREADS 5

void * BusyWork(void * arg)
{
    printf("Thread %d starting ...\n", (int)arg);
    double result = 0.0;
    for (int i = 0; i < 1000000; i++)
    {
        result = result + sin(i) * tan(i);
    }
    printf("Thread %d done, result = %e\n", (int)arg, result);
    pthread_exit(arg);
}

int main()
{
    int iRet;
    int i;

    pthread_attr_t attr;
    pthread_attr_init(&attr);
    pthread_attr_setdetachstate(&attr, PTHREAD_CREATE_JOINABLE);
    pthread_t threads[NUM_THREADS];
    for (i = 0; i < NUM_THREADS; i++)
    {
        printf("In main: creating thead %d\n", i);
        iRet = pthread_create(&threads[i], &attr, BusyWork, (void*)i);
        if(iRet != 0) {
            printf("Error: pthread_create failed = %d\n", iRet);
            exit(-1);
        }
    }

    pthread_attr_destroy(&attr);
    for (i = 0; i < NUM_THREADS; i++)
    {
        void *status = NULL;
        iRet = pthread_join(threads[i], &status);
        if(iRet != 0) {
            printf("Error: pthread_join failed = %d\n", iRet);
            exit(-1);
        }
        printf("In main: completed join with thread %d status %d\n", i, (int)status);
    }
    printf("In main: program completed. Exiting.\n");
    pthread_exit(NULL);
    return 0;
}
```
