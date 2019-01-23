---
layout: post
title: "[FreeRTOS] Queueset"
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

void vSenderTask1(void *pvParameters);
void vSenderTask2(void *pvParameters);
void vReceiverTask(void *pvParameters);

static QueueHandle_t xQueue1 = NULL;
static QueueHandle_t xQueue2 = NULL;
static QueueSetHandle_t xQueueSet = NULL;

int main()
{
	xQueue1 = xQueueCreate(1, sizeof(char*));
	xQueue2 = xQueueCreate(1, sizeof(char*));

	xQueueSet = xQueueCreateSet(1 * 2);

	xQueueAddToSet(xQueue1, xQueueSet);
	xQueueAddToSet(xQueue2, xQueueSet);

	xTaskCreate(vSenderTask1, "Sender1", 1000, NULL, 1, NULL);
	xTaskCreate(vSenderTask1, "Sender2", 1000, NULL, 1, NULL);

	xTaskCreate(vReceiverTask, "Receiver", 1000, NULL, 2, NULL);

	vTaskStartScheduler();

	for (;;);

	return 0;
}

void vSenderTask1(void *pvParameters)
{
	const TickType_t xBlockTime = pdMS_TO_TICKS(1000);
	const char * const pcMessage = "Message from vSenderTask1\n";

	for (;;)
	{
		vTaskDelay(xBlockTime);
		xQueueSend(xQueue1, &pcMessage, 0);
	}
}
void vSenderTask2(void *pvParameters)
{
	const TickType_t xBlockTime = pdMS_TO_TICKS(2000);
	const char * const pcMessage = "Message from vSenderTask2\n";

	for (;;)
	{
		vTaskDelay(xBlockTime);
		xQueueSend(xQueue2, &pcMessage, 0);
	}
}
void vReceiverTask(void *pvParameters)
{
	QueueHandle_t xQueueThatContainsData;
	char * pcReceivedString;

	for (;;)
	{
		xQueueThatContainsData = (QueueHandle_t)xQueueSelectFromSet(xQueueSet, portMAX_DELAY);

		xQueueReceive(xQueueThatContainsData, &pcReceivedString, 0);

		vPrintString(pcReceivedString);
	}
}
```

