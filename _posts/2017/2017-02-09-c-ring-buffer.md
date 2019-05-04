---
layout: post
title:  "[C/C++] Ring buffer"
date:   2017-02-09
comments: true
categories: C/C++
tags: [C]
description: ""
published: true
---

```cpp
#include "dcclientcache.h"

#define CACHE_MAX_SIZE 32*1024

typedef struct _SDCRing
{
    char buf[CACHE_MAX_SIZE];
    uint32_t size;
    uint32_t in;
    uint32_t out;
    uint32_t send;
}SDCRing;

SDCRing xDCRing;

void vRingInit()
{
    memset(xDCRing.buf, 0, sizeof(xDCRing.buf));
    xDCRing.size = CACHE_MAX_SIZE;
    xDCRing.in = 0;
    xDCRing.out = 0;
}

uint32_t uiRingPut(char * pcBuf, uint32_t uiLen)
{
    if(uiLen > xDCRing.size - xDCRing.in + xDCRing.out)
    {
        LOG_PRINTF(EWarn, DCClient, "Cache left space in no enough.\n");
        return 0;
    }

    uint32_t uil;
    uiLen = min(uiLen, xDCRing.size - xDCRing.in + xDCRing.out);
    uil = min(uiLen, xDCRing.size - (xDCRing.in & (xDCRing.size - 1)));
    memcpy(xDCRing.buf + (xDCRing.in & (xDCRing.size - 1)), pcBuf, uil);
    memcpy(xDCRing.buf, pcBuf + uil, uiLen - uil);
    xDCRing.in += uiLen;
    return uiLen;
}

uint32_t uiRingGetSend(char * pcBuf, uint32_t uiLen)
{
    uint32_t uil;
    uiLen = min(uiLen, xDCRing.in - xDCRing.send);
    uil = min(uiLen, xDCRing.size - (xDCRing.send & (xDCRing.size - 1)));
    memcpy(pcBuf, xDCRing.buf + (xDCRing.send & (xDCRing.size - 1)), uil);
    memcpy(pcBuf + uil, xDCRing.buf, uiLen - uil);
    xDCRing.send += uiLen;
    return uiLen;
}

uint32_t uiRingGetOut(char * pcBuf, uint32_t uiLen)
{
    uint32_t uil;
    uiLen = min(uiLen, xDCRing.in - xDCRing.out);
    uil = min(uiLen, xDCRing.size - (xDCRing.out & (xDCRing.size - 1)));
    memcpy(pcBuf, xDCRing.buf + (xDCRing.out & (xDCRing.size - 1)), uil);
    memcpy(pcBuf + uil, xDCRing.buf, uiLen - uil);
    return uiLen;
}

uint32_t uiRingDel(uint32_t uiLen)
{
    uiLen = min(uiLen, xDCRing.in - xDCRing.out);
    xDCRing.out += uiLen;
    return uiLen;
}

void vRingInitSend()
{
    xDCRing.send = xDCRing.out;
}

```
