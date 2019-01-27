---
layout: post
title: "[FreeRTOS] Queue Create"
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


static void vSenderTask(void * pvParameters);
static void vReceiverTask(void * pvParameters);

QueueHandle_t xQueue;

int main()
{
	xQueue = xQueueCreate(5, sizeof(int32_t));

	if (xQueue != NULL)
	{
		xTaskCreate(vSenderTask, "Sender1", 1000, (void*)100, 1, NULL);
		xTaskCreate(vSenderTask, "Sender2", 1000, (void*)200, 1, NULL);

		xTaskCreate(vReceiverTask, "Receiver", 1000, NULL, 2, NULL);
	
		vTaskStartScheduler();
	}
	else
	{

	}

	for (;;);

	return 0;
}


static void vSenderTask(void * pvParameters)
{
	int32_t lValueToSend;
	BaseType_t xStatus;

	lValueToSend = (int32_t)pvParameters;

	for (;;)
	{
		xStatus = xQueueSendToBack(xQueue, &lValueToSend, 0);
		if (xStatus != pdPASS)
		{
			vPrintString("Could not send to the queue.\n");
		}
	}
}

static void vReceiverTask(void * pvParameters)
{
	int32_t lReceivedValue;
	BaseType_t xStatus;
	const TickType_t xTicksToWait = pdMS_TO_TICKS(100UL);

	for (;;)
	{
		UBaseType_t xItemNum = uxQueueMessagesWaiting(xQueue);
		printf("Queue item number = %d\n", xItemNum);

		xStatus = xQueueReceive(xQueue, &lReceivedValue, xTicksToWait);

		if (xStatus == pdPASS)
		{
			vPrintStringAndNumber("Received = ", lReceivedValue);
		}
		else
		{
			vPrintString("Could not receive from the queue.\n");
		}
	}
}
```


