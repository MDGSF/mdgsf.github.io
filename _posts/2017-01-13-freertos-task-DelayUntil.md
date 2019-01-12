---
layout: post
title: "[FreeRTOS] vTaskDelayUntil"
date: 2017-01-13
author: mdgsf
comments: true
categories: FreeRTOS
tags: FreeRTOS
description:
published: true #default true
---


```cpp
#include "FreeRTOS.h"
#include "task.h"
#include "queue.h"
#include "list.h"
#include "supporting_functions.h"

#define mainDELAY_LOOP_COUNT (0xffffff)

void vTaskFunction(void * pvParameters);

const char * pcTextForTask1 = "Task 1 is running\n";
const char * pcTextForTask2 = "Task 2 is running\n";


int main(void)
{
	xTaskCreate(vTaskFunction, "Task1", 1000, (void*)pcTextForTask1, 1, NULL);
	xTaskCreate(vTaskFunction, "Task2", 1000, (void*)pcTextForTask2, 2, NULL);

	vTaskStartScheduler();

	for (;;);

	return 0;
}

void vTaskFunction(void * pvParameters)
{
	char * pcTaskName = (char*)pvParameters;
	const TickType_t xDelay250ms = pdMS_TO_TICKS(250UL);
	TickType_t xLastWakeTime = xTaskGetTickCount();

	for (;;)
	{
		vPrintString(pcTaskName);
		vTaskDelayUntil(&xLastWakeTime, xDelay250ms);
	}
}
```

