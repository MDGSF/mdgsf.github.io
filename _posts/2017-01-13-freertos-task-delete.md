---
layout: post
title: "[FreeRTOS] Task Delete"
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

void vTask1(void * pvParameters);
void vTask2(void * pvParameters);

TaskHandle_t xTask2Handle;

int main()
{
	xTaskCreate(vTask1, "Task 1", 1000, NULL, 1, NULL);

	vTaskStartScheduler();

	for (;;);

	return 0;
}

void vTask1(void * pvParameters)
{
	const TickType_t xDelay1000ms = pdMS_TO_TICKS(1000UL);
	for (;;)
	{
		vPrintString("Task1 is running\n");

		xTaskCreate(vTask2, "Task 2", 1000, NULL, 1, &xTask2Handle);

		vTaskDelay(xDelay1000ms);
	}
}

void vTask2(void * pvParameters)
{
	vPrintString("Task2 is running and about to delete itself\n");

	vTaskDelete(xTask2Handle);
}
```


