---
layout: post
title:  "[pthread] stack size"
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

#define NUM_THREADS 5
#define N 1000
#define MEGEXTRA 1000000

pthread_attr_t attr;

void * DoWork(void * arg)
{
    size_t iStackSize;
    pthread_attr_getstacksize(&attr, &iStackSize);
    printf("Thread %d, stack size = %d bytes\n", (int)arg, iStackSize);
    double A[N][N];
    for (int i = 0; i < N; i++)
        for (int j = 0; j < N; j++)
            A[i][j] = ((i*j)/3.452) + (N-i);
    pthread_exit(NULL);
}

int main()
{
    int iRet;
    int i;

    pthread_attr_init(&attr);

    size_t iStackSize;
    pthread_attr_getstacksize(&attr, &iStackSize);
    printf("Default stack size = %d\n", iStackSize);

    iStackSize = sizeof(double)*N*N + MEGEXTRA;
    printf("New stack size = %d\n", iStackSize);
    pthread_attr_setstacksize(&attr, iStackSize);

    pthread_t threads[NUM_THREADS];
    for (i = 0; i < NUM_THREADS; i++)
    {
        iRet = pthread_create(&threads[i], &attr, DoWork, (void*)i);
        if(iRet != 0) {
            printf("Error: pthread_create failed = %d\n", iRet);
            exit(-1);
        }
    }
    printf("Created %d threads.\n", i);
    pthread_exit(NULL);
    return 0;
}
```
