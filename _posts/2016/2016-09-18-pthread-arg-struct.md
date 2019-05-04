---
layout: post
title:  "[pthread] use struct as arguments"
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

#define NUM_THREADS 2

struct thread_data {
    int thread_id;
    int sum;
    const char * message;
};

struct thread_data thread_data_array[NUM_THREADS] = {
    {1, 1, "thread 1"},
    {2, 2, "thread 2"}
};

void * PrintHello(void * arg)
{
    struct thread_data * pData = (struct thread_data*)arg;
    printf("Hello World! It's me, %d:%s\n", pData->thread_id, pData->message);
    pthread_exit(NULL);
}

int main()
{
    pthread_t threads[NUM_THREADS];
    for (int i = 0; i < NUM_THREADS; i++)
    {
        printf("In main: creating thead %d\n", i);
        int iRet = pthread_create(&threads[i], NULL, PrintHello, (void*)&thread_data_array[i]);
        if(iRet != 0) {
            printf("Error: pthread_create failed = %d\n", iRet);
            exit(-1);
        }
    }

    pthread_exit(NULL);
    return 0;
}
```
