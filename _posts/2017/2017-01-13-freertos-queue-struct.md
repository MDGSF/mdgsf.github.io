---
layout: post
title: "[FreeRTOS] Queue struct"
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

typedef enum
{
    eSender1,
    eSender2
} DataSource_t;

typedef struct
{
    uint8_t ucValue;
    DataSource_t eDataSource;
} Data_t;

static const Data_t xStructToSend[2] =
{
    {100, eSender1},
    {200, eSender2}
};

int main()
{
    xQueue = xQueueCreate(3, sizeof(Data_t));
    if (xQueue != NULL)
    {
        xTaskCreate(vSenderTask, "Sender1", 1000, (void*)&(xStructToSend[0]), 2, NULL);
        xTaskCreate(vSenderTask, "Sender2", 1000, (void*)&(xStructToSend[1]), 2, NULL);

        xTaskCreate(vReceiverTask, "Receiver", 1000, NULL, 1, NULL);

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
    BaseType_t xStatus;
    const TickType_t xTicksToWait = pdMS_TO_TICKS(100UL);

    for (;;)
    {
        xStatus = xQueueSendToBack(xQueue, pvParameters, xTicksToWait);
        if (xStatus != pdPASS)
        {
            vPrintString("Could not send to the queue.\n");
        }
    }
}

static void vReceiverTask(void * pvParameters)
{
    Data_t xReceivedStructure;
    BaseType_t xStatus;

    for (;;)
    {
        if (uxQueueMessagesWaiting(xQueue) != 3)
        {
            vPrintString("Queue should have been full\n");
        }

        xStatus = xQueueReceive(xQueue, &xReceivedStructure, 0);
        if (xStatus == pdPASS)
        {
            if (xReceivedStructure.eDataSource == eSender1)
            {
                vPrintStringAndNumber("From Sender 1 = ", xReceivedStructure.ucValue);
            }
            else
            {
                vPrintStringAndNumber("From Sender 2 = ", xReceivedStructure.ucValue);
            }
        }
        else
        {
            vPrintString("Could not receive from the queue.\n");
        }
    }
}
```
