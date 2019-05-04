---
layout: post
title:  "[pthread] skeleton"
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

void * PrintHello(void * arg)
{
    int iPid = (int)arg;
    printf("Hello World! It's me, thread %d\n", iPid);
    pthread_exit(NULL);
}

int main()
{
    pthread_t threads[NUM_THREADS];
    for (int i = 0; i < NUM_THREADS; i++)
    {
        printf("In main: creating thead %d\n", i);
        int iRet = pthread_create(&threads[i], NULL, PrintHello, (void*)i);
        if(iRet != 0) {
            printf("Error: pthread_create failed = %d\n", iRet);
            exit(-1);
        }
    }

    pthread_exit(NULL);
    return 0;
}
```
