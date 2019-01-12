---
layout: post
title: "[FreeRTOS] Periodic task"
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

void vContinuousProcessingTask(void * pvParameters);
void vPeriodicTask(void * pvParameters);

const char * pcTextForTask1 = "Continuous task 1 is running\n";
const char * pcTextForTask2 = "Continuous task 2 is running\n";
const char * pcTextForPeriodicTask = "Periodic task is running\n";

int main(void)
{
	xTaskCreate(vContinuousProcessingTask, "Task 1", 1000, (void*)pcTextForTask1, 1, NULL);
	xTaskCreate(vContinuousProcessingTask, "Task 2", 1000, (void*)pcTextForTask2, 1, NULL);
	
	xTaskCreate(vPeriodicTask, "Task 3", 1000, (void*)pcTextForPeriodicTask, 2, NULL);

	vTaskStartScheduler();

	for (;;);

	return 0;
}

void vContinuousProcessingTask(void * pvParameters)
{
	char * pcTaskName = (char*)pvParameters;

	for (;;)
	{
		vPrintString(pcTaskName);
	}
}

void vPeriodicTask(void * pvParameters)
{
	const TickType_t xDelay250ms = pdMS_TO_TICKS(250UL);
	TickType_t xLastWakeTime = xTaskGetTickCount();

	for (;;)
	{
		vPrintString("Periodic task is running\n");
		vTaskDelayUntil(&xLastWakeTime, xDelay250ms);
	}
}
```

