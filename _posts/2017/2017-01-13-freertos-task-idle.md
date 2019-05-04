---
layout: post
title: "[FreeRTOS] Idle task"
date: 2017-01-13
author: mdgsf
comments: true
categories: FreeRTOS
tags: [Freertos]
description:
published: true #default true
---

```cpp
#include "FreeRTOS.h"
#include "task.h"
#include "queue.h"
#include "list.h"
#include "supporting_functions.h"

void vTaskFunction(void * pvParameters);

static uint32_t ulIdleCycleCount = 0UL;

const char * pcTextForTask1 = "Task 1 is running\n";
const char * pcTextForTask2 = "Task 2 is running\n";

int main()
{
    xTaskCreate(vTaskFunction, "Task 1", 1000, (void*)pcTextForTask1, 1, NULL);

    xTaskCreate(vTaskFunction, "Task 2", 1000, (void*)pcTextForTask2, 2, NULL);

    vTaskStartScheduler();

    for (;;);

    return 0;
}

void vTaskFunction(void * pvParameters)
{
    char * pcTaskName = (char*)pvParameters;
    const TickType_t xDelay250ms = pdMS_TO_TICKS(250UL);

    for (;;)
    {
        vPrintStringAndNumber(pcTaskName, ulIdleCycleCount);

        vTaskDelay(xDelay250ms);
    }
}

void vApplicationIdleHook(void)
{
    ulIdleCycleCount++;
}
```

```
FreeRTOSConfig.h
#define configUSE_IDLE_HOOK 1
```
