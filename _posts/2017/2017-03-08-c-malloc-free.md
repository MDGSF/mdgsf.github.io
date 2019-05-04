---
layout: post
title:  "[C/C++] malloc 实现"
date:   2017-03-08
comments: true
categories: C/C++
tags: [C,Strcat]
description:
published: true
---

摘自 FreeRTOS 的 heap5.c

```cpp
#ifndef HEAP_HJ
#define HEAP_HJ

#include <stdio.h>
#include <stdint.h>

#define portBYTE_ALIGNMENT 8

#if portBYTE_ALIGNMENT == 8
    #define portBYTE_ALIGNMENT_MASK ( 0x0007U )
#endif

#if portBYTE_ALIGNMENT == 4
    #define portBYTE_ALIGNMENT_MASK ( 0x0003 )
#endif

#if portBYTE_ALIGNMENT == 2
    #define portBYTE_ALIGNMENT_MASK ( 0x0001 )
#endif

#if portBYTE_ALIGNMENT == 1
    #define portBYTE_ALIGNMENT_MASK ( 0x0000 )
#endif

#define configTOTAL_HEAP_SIZE 256*1024

#define configASSERT( x )

#define vTaskSuspendAll()
#define xTaskResumeAll()

#define mtCOVERAGE_TEST_MARKER()

typedef struct _SHeapRegion
{
    uint8_t * pucStartAddress;
    size_t xSizeInBytes;
}SHeapRegion;


void vPortDefineHeapRegions(const SHeapRegion * const pxHeapRegions);

void * pvPortMalloc(size_t xWantedSize);

void vPortFree(void * pv);

size_t xPortGetFreeHeapSize(void);

size_t xPortGetMinimumEverFreeHeapSize(void);

void dump_mem_block_list();

#endif
```

```cpp
#include "heap.h"


/* Block sizes must not get too small. */
#define heapMINIMUM_BLOCK_SIZE ( (size_t) (uxHeapStructSize << 1) )

/* Assumes 8bit bytes! */
#define heapBITS_PER_BYTE ( (size_t) 8 )

/* Define the linked list structure. This is used to link free blocks in order
 * of their memory address. */
typedef struct _SBlock
{
    struct _SBlock * pxNextFreeBlock;
    size_t xBlockSize;
}SBlock;

static const uint32_t uxHeapStructSize = ( ( sizeof(SBlock) + (portBYTE_ALIGNMENT - 1) ) & ~portBYTE_ALIGNMENT_MASK );

static SBlock xStart;
static SBlock * pxEnd = NULL;

static size_t xFreeBytesRemaining = 0;
static size_t xMinimumEverFreeBytesRemaining = 0;

static size_t xBlockAllocatedBit = 0;


static uint8_t ucHeap[configTOTAL_HEAP_SIZE];

SHeapRegion xHeapRegions[] =
{
    { ucHeap, sizeof(ucHeap) },
    { NULL, 0 } // Terminates the array.
};


static void s_vInsertBlockIntoFreeList(SBlock * pxBlockToInsert);



void vPortDefineHeapRegions(const SHeapRegion * const pxHeapRegions)
{
    /* can only call once! */
    configASSERT( pxEnd == NULL );

    const SHeapRegion * pxHeapRegion = NULL;
    uint32_t uiAddress = 0;
    uint8_t * pucAlignedHeap = NULL;
    size_t xTotalRegionSize = 0;
    SBlock * pxFirstFreeBlockInRegion = NULL;

    uint32_t uiDefinedRegions = 0;
    size_t xTotalHeapSize = 0;
    SBlock * pxPreviousFreeBlock = NULL;

    pxHeapRegion = &pxHeapRegions[uiDefinedRegions];
    while (pxHeapRegion->xSizeInBytes > 0)
    {
        xTotalRegionSize = pxHeapRegion->xSizeInBytes;
        uiAddress = (uint32_t)pxHeapRegion->pucStartAddress;

        /* Ensure the heap region starts on a correctly aligned boundary. */
        if( (uiAddress & portBYTE_ALIGNMENT_MASK) != 0 )
        {
            uiAddress += (portBYTE_ALIGNMENT - 1);
            uiAddress &= ~portBYTE_ALIGNMENT_MASK;

            xTotalRegionSize -= uiAddress - (uint32_t)pxHeapRegion->pucStartAddress;
        }

        pucAlignedHeap = (uint8_t*)uiAddress;

        /* Set start if it has not already been set. */
        if(uiDefinedRegions == 0)
        {
            xStart.pxNextFreeBlock = (SBlock*)pucAlignedHeap;
            xStart.xBlockSize = (size_t)0;
        }
        else
        {
            configASSERT( pxEnd != NULL );

            /* check blocks are passed in with increasing start addresses. */
            configASSERT( uiAddress > (uint32_t)pxEnd );
        }

        /* Remember the location of the end marker in the previous region, if any. */
        pxPreviousFreeBlock = pxEnd;

        /* pxEnd is used to mark the end of the list of free blocks and is
         * inserted at the end of the region space. */
        uiAddress = ( (uint32_t) pucAlignedHeap ) + xTotalRegionSize;
        uiAddress -= uxHeapStructSize;
        uiAddress &= ~portBYTE_ALIGNMENT_MASK;
        pxEnd = (SBlock*)uiAddress;
        pxEnd->pxNextFreeBlock = NULL;
        pxEnd->xBlockSize = 0;

        pxFirstFreeBlockInRegion = (SBlock*)pucAlignedHeap;
        pxFirstFreeBlockInRegion->pxNextFreeBlock = pxEnd;
        pxFirstFreeBlockInRegion->xBlockSize = uiAddress - (uint32_t)pxFirstFreeBlockInRegion;

        /* If this is not the first region that makes up the entire heap space
         * then link the previous region to this region. */
        if(pxPreviousFreeBlock != NULL)
        {
            pxPreviousFreeBlock->pxNextFreeBlock = pxFirstFreeBlockInRegion;
        }

        xTotalHeapSize += pxFirstFreeBlockInRegion->xBlockSize;

        uiDefinedRegions++;
        pxHeapRegion = &pxHeapRegions[uiDefinedRegions];
    }

    xFreeBytesRemaining = xTotalHeapSize;
    xMinimumEverFreeBytesRemaining = xTotalHeapSize;

    configASSERT( xTotalHeapSize );

    /* work out the position of the top bit in a size_t variable. */
    xBlockAllocatedBit = ( (size_t) 1 ) << ((sizeof(size_t) * heapBITS_PER_BYTE) - 1);
}

void * pvPortMalloc(size_t xWantedSize)
{
    if(NULL == pxEnd)
    {
        vPortDefineHeapRegions(xHeapRegions);
    }
    configASSERT(pxEnd != NULL);

    if(xWantedSize <= 0) {
        printf("pvPortMalloc xWantedSize <= 0\n");
        return NULL;
    }

    /* Check the requested block size is not so large that the top bit is set. */
    if( (xWantedSize & xBlockAllocatedBit) != 0 ) {
        printf("pvPortMalloc xWantedSize is to large\n");
        return NULL;
    }

    SBlock * pxBlock = NULL;
    SBlock * pxPreviousBlock = NULL;
    SBlock * pxNewBlockLink = NULL;
    void * pvReturn = NULL;

    vTaskSuspendAll();
    {
        /* The wanted size is increased so it can contain a SBlock
         * structure in addition to the requested amount of bytes. */
        xWantedSize += uxHeapStructSize;

        /* Ensure that blocks are always aligned to the required number of bytes. */
        if( (xWantedSize & portBYTE_ALIGNMENT_MASK) != 0x00 ) {
            xWantedSize += (portBYTE_ALIGNMENT - (xWantedSize & portBYTE_ALIGNMENT_MASK));
        }

        if( (xWantedSize > 0) && (xWantedSize <= xFreeBytesRemaining) )
        {
            /* Traverse the list from the start (lowest address) block until
             * one of adequate size is found. */
            pxPreviousBlock = &xStart;
            pxBlock = xStart.pxNextFreeBlock;
            while ( (pxBlock->xBlockSize < xWantedSize) && (pxBlock->pxNextFreeBlock != NULL) )
            {
                pxPreviousBlock = pxBlock;
                pxBlock = pxBlock->pxNextFreeBlock;
            }

            if(pxBlock != pxEnd)
            {
                /* Return the memory space pointed to - jumping over the
                 * SBlock structure at its start. */
                pvReturn = (void*) (((uint8_t*)pxPreviousBlock->pxNextFreeBlock) + uxHeapStructSize);

                /* This block is being returned for use so must be taken out
                 * of the list of free blocks. */
                pxPreviousBlock->pxNextFreeBlock = pxBlock->pxNextFreeBlock;

                /* If the block is larger than required it can be split into two. */
                if( (pxBlock->xBlockSize - xWantedSize) > heapMINIMUM_BLOCK_SIZE )
                {
                    pxNewBlockLink = (SBlock*)(((uint8_t*)pxBlock) + xWantedSize);
                    pxNewBlockLink->xBlockSize = pxBlock->xBlockSize - xWantedSize;
                    pxBlock->xBlockSize = xWantedSize;
                    s_vInsertBlockIntoFreeList(pxNewBlockLink);
                }

                xFreeBytesRemaining -= pxBlock->xBlockSize;
                if(xFreeBytesRemaining < xMinimumEverFreeBytesRemaining) {
                    xMinimumEverFreeBytesRemaining = xFreeBytesRemaining;
                }

                /* The block is being returned - it is allocated and owned
                 * by the application and has no "next" block. */
                pxBlock->pxNextFreeBlock = NULL;
                pxBlock->xBlockSize |= xBlockAllocatedBit;
            }
        }
    }
    xTaskResumeAll();

    return pvReturn;
}

void vPortFree(void * pv)
{
    if(NULL == pv) {
        printf("vPortFree NULL == pv\n");
        return ;
    }

    uint8_t * puc = (uint8_t*)pv;
    SBlock * pxLink = NULL;

    /* The memory being freed will have an SBlock structure immediately before it. */
    puc -= uxHeapStructSize;
    pxLink = (SBlock*)puc;

    configASSERT( pxLink->pxNextFreeBlock == NULL );
    configASSERT( (pxLink->xBlockSize & xBlockAllocatedBit) != 0 );

    if( !( ((pxLink->xBlockSize & xBlockAllocatedBit) != 0) && (pxLink->pxNextFreeBlock == NULL) ) )
    {
        return ;
    }

    pxLink->xBlockSize &= ~xBlockAllocatedBit;

    vTaskSuspendAll();
    {
        xFreeBytesRemaining += pxLink->xBlockSize;
        s_vInsertBlockIntoFreeList( pxLink );
    }
    xTaskResumeAll();
}

size_t xPortGetFreeHeapSize(void)
{
    return xFreeBytesRemaining;
}

size_t xPortGetMinimumEverFreeHeapSize(void)
{
    return xMinimumEverFreeBytesRemaining;
}



static void
s_vInsertBlockIntoFreeList(SBlock * pxBlockToInsert)
{
    SBlock * pxIterator = NULL;
    uint8_t * puc = NULL;

    /* Iterate through the list until a block is found that has a higher address
     * than the block being inserted. */
    for (pxIterator = &xStart;
        pxIterator->pxNextFreeBlock < pxBlockToInsert;
        pxIterator = pxIterator->pxNextFreeBlock)
    {
        /* Nothing to do here, just iterate to the right position. */
    }

    /* Merge with previous block. */
    puc = (uint8_t*)pxIterator;
    if( (puc + pxIterator->xBlockSize) == (uint8_t*)pxBlockToInsert )
    {
        pxIterator->xBlockSize += pxBlockToInsert->xBlockSize;
        pxBlockToInsert = pxIterator;
    }
    else
    {
        mtCOVERAGE_TEST_MARKER();
    }

    /* Merge with next block. */
    puc = (uint8_t*)pxBlockToInsert;
    if( (puc + pxBlockToInsert->xBlockSize) == (uint8_t*)pxIterator->pxNextFreeBlock )
    {
        if(pxIterator->pxNextFreeBlock != pxEnd)
        {
            pxBlockToInsert->pxNextFreeBlock = pxIterator->pxNextFreeBlock->pxNextFreeBlock;
            pxBlockToInsert->xBlockSize += pxIterator->pxNextFreeBlock->xBlockSize;
        }
        else
        {
            pxBlockToInsert->pxNextFreeBlock = pxEnd;
        }
    }
    else
    {
        pxBlockToInsert->pxNextFreeBlock = pxIterator->pxNextFreeBlock;
    }


    if(pxIterator != pxBlockToInsert)
    {
        pxIterator->pxNextFreeBlock = pxBlockToInsert;
    }
    else
    {
        mtCOVERAGE_TEST_MARKER();
    }
}

#if 1
void dump_mem_block_list()
{
    SBlock * pxBlock = &xStart;
    int count = 0;

    printf("=============================>Memory List:\n");
    printf("uxHeapStructSize = %u\n", uxHeapStructSize);
    printf("current free heap size: %u\n", xPortGetFreeHeapSize());
    printf("pxEnd = %p\n", pxEnd);

    printf("xStart.pxNextFreeBlock = %p\n", xStart.pxNextFreeBlock);
    while (pxBlock->pxNextFreeBlock != NULL)
    {
        printf("[%d]=%p, %d\n", count++, pxBlock, pxBlock->xBlockSize);
        pxBlock = pxBlock->pxNextFreeBlock;
    }
    printf("\n");
}
#endif
```
