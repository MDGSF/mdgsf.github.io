---
layout: post
title:  "[pthread] mutex"
date:   2016-09-18
comments: true
categories: pthread
tags: [Pthread]
description:
published: true
---

```cpp
#include <stdio.h>
#include <stdlib.h>
#include <pthread.h>
#include <math.h>

//if this two num is too small, even you comment the pthread_mutex_lock
//and pthread_mutex_unlock, you may be can't see the difference.
#define NUM_THREADS 80
#define VECLEN 100000

typedef struct
{
    double * a;
    double * b;
    double sum;
    int veclen;
}DOTDATA;

DOTDATA g_dotstr;
pthread_t threads[NUM_THREADS];
pthread_mutex_t mutex;

void * dotprod(void * arg)
{
    double * x = g_dotstr.a;
    double * y = g_dotstr.b;

    int iOffset = (int)arg;
    int iLen = g_dotstr.veclen;
    int iStart = iOffset * iLen;
    int iEnd = iStart + iLen;
    for (int i = iStart; i < iEnd; i++)
    {
        pthread_mutex_lock(&mutex);
        g_dotstr.sum += (x[i] * y[i]);
        pthread_mutex_unlock(&mutex);
    }

    pthread_exit((void*)0);
}

int main()
{
    int iRet;
    int i;

    double * a = (double*)malloc(NUM_THREADS * VECLEN * sizeof(double));
    double * b = (double*)malloc(NUM_THREADS * VECLEN * sizeof(double));

    for (i = 0; i < NUM_THREADS * VECLEN; i++)
    {
        a[i] = 1.0;
        b[i] = a[i];
    }

    g_dotstr.a = a;
    g_dotstr.b = b;
    g_dotstr.sum = 0.0;
    g_dotstr.veclen = VECLEN;

    pthread_mutex_init(&mutex, NULL);

    pthread_attr_t attr;
    pthread_attr_init(&attr);
    pthread_attr_setdetachstate(&attr, PTHREAD_CREATE_JOINABLE);
    for (i = 0; i < NUM_THREADS; i++)
    {
        iRet = pthread_create(&threads[i], &attr, dotprod, (void*)i);
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
    }
    printf("In main: program completed. Sum = %lf.\n", g_dotstr.sum);
    pthread_mutex_destroy(&mutex);
    pthread_exit(NULL);
    return 0;
}
```
