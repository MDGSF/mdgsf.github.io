---
layout: post
title:  "[pthread] condition"
date:   2016-09-18
comments: true
categories: pthread
tags: [Pthread]
description:
published: true
---



```
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
#include <pthread.h>

#define NUM_THREADS 3
#define TCOUNT 10
#define COUNT_LIMIT 12

int count = 0;
pthread_mutex_t count_mutex;
pthread_cond_t count_threshold_cv;

void * inc_count(void * arg)
{
    for (int i = 0; i < TCOUNT; i++)
    {
        pthread_mutex_lock(&count_mutex);
        count++;
        if(count == COUNT_LIMIT)
        {
            pthread_cond_signal(&count_threshold_cv);
            printf("inc_count(): thread %d count = %d, thresold reached.\n", (int)arg, count);
        }
        printf("inc_count(): thread %d count = %d\n", (int)arg, count);
        pthread_mutex_unlock(&count_mutex);
        sleep(1);
    }
    pthread_exit(NULL);
}

void * watch_count(void * arg)
{
    printf("Starting watch_count(): thread %d\n", (int)arg);
    pthread_mutex_lock(&count_mutex);
    while (count < COUNT_LIMIT) {
        pthread_cond_wait(&count_threshold_cv, &count_mutex);
        printf("watch_count(): thread %d condition signal received.\n", (int)arg);
        count += 125;
        printf("watch_count(): thread %d count now = %d\n", (int)arg, count);
    }
    pthread_mutex_unlock(&count_mutex);
    pthread_exit(NULL);
}

int main()
{
    int iRet;
    int i;

    pthread_t threads[NUM_THREADS];

    pthread_mutex_init(&count_mutex, NULL);
    pthread_cond_init(&count_threshold_cv, NULL);

    pthread_attr_t attr;
    pthread_attr_init(&attr);
    pthread_attr_setdetachstate(&attr, PTHREAD_CREATE_JOINABLE);

    pthread_create(&threads[0], &attr, watch_count, (void*)0);
    pthread_create(&threads[1], &attr, inc_count, (void*)1);
    pthread_create(&threads[2], &attr, inc_count, (void*)2);

    for (i = 0; i < NUM_THREADS; i++)
    {
        iRet = pthread_join(threads[i], NULL);
        if(iRet != 0) {
            printf("Error: pthread_join failed = %d\n", iRet);
            exit(-1);
        }
    }
    printf("In main: program completed. Exiting.\n");

    pthread_attr_destroy(&attr);
    pthread_mutex_destroy(&count_mutex);
    pthread_cond_destroy(&count_threshold_cv);
    pthread_exit(NULL);
    return 0;
}
```

