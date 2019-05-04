---
layout: post
title: "[FreeRTOS] Task Priority"
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

void vTask1(void * pvParameters);
void vTask2(void * pvParameters);

TaskHandle_t xTask2Handle;

int main()
{
    xTaskCreate(vTask1, "Task 1", 1000, NULL, 2, NULL);

    xTaskCreate(vTask2, "Task 2", 1000, NULL, 1, &xTask2Handle);

    vTaskStartScheduler();

    for (;;);

    return 0;
}

void vTask1(void * pvParameters)
{
    UBaseType_t uxPriority = uxTaskPriorityGet(NULL);
    for (;;)
    {
        vPrintString("Task1 is running\n");
        vPrintString("About to raise the Task2 priority\n");
        vTaskPrioritySet(xTask2Handle, (uxPriority + 1));
    }
}

void vTask2(void * pvParameters)
{
    UBaseType_t uxPriority = uxTaskPriorityGet(NULL);
    for (;;)
    {
        vPrintString("Task2 is running\n");
        vPrintString("About to lower the Task2 priority\n");
        vTaskPrioritySet(NULL, (uxPriority - 2));
    }
}
```
