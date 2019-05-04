---
layout: post
title:  "[C/C++] hashtable"
date:   2016-07-01
comments: true
categories: C/C++
tags: [C++,Hashtable]
description:
published: true
---

冲突解决技术可以分为两类：**开散列方法**( open hashing，也称为拉链法，separate chaining )和**闭散列方法**( closed hashing，也称为开地址方法，open addressing )。这两种方法的不同之处在于：开散列法把发生冲突的关键码存储在散列表主表之外，而闭散列法把发生冲突的关键码存储在表中另一个槽内。

这里使用的是开散列。

A simple hashtabl.

```cpp
#ifndef HASHTABLE_HJ
#define HASHTABLE_HJ

#include <stdio.h>
#include <string.h>

#define DEFAULT_BUCKET_SIZE 12000

namespace JCL
{
    namespace pub
    {

        enum EHashType {
            EDJBHASH,
            EAPHASH
        };

        typedef struct _SNode {
            struct _SNode * m_pNext;
            char m_acKey[BUFSIZ];
            void * m_pvVal;
            _SNode(): m_pNext(NULL), m_pvVal(NULL)
            {
                memset(m_acKey, 0, sizeof(m_acKey));
            }
            ~_SNode()
            {
            }
        }SNode;

        class CHashTable
        {
            public:
                CHashTable(
                        EHashType iHashType = EDJBHASH,
                        int iMaxBucketSize = DEFAULT_BUCKET_SIZE);

                virtual ~CHashTable();

                /*
                 * @param pcKey[IN]: the string key
                 * @return the value, failed: NULL.
                 */
                void * m_pvFind(const char * pcKey);

                /*
                 * @param pcKey[IN]: use string as key
                 * @param pvVal[IN]: use to store the value.
                 * @return 0:success, -1:failed.
                 */
                int m_iInsert(const char * pcKey, void * pvVal);


                /*
                 * @param pcKey[IN]: the string key
                 * @return 0:success, -1:failed.
                 */
                int m_iDelete(const char * pcKey);

                /*
                 * get the current size of hashtable.
                 */
                int m_iGetSize();

                void m_vShowStatus();

            private:
                CHashTable(const CHashTable&);
                CHashTable& operator = (const CHashTable&);

                SNode * m_pstFindNode(const char * pcKey);

                unsigned int m_iHashFunction(const char * pcKey);
                unsigned int DJBHash(const char * str);
                unsigned int APHash(const char* str);
                EHashType m_iHashType;

                int m_iSize;
                int m_iMaxBucketSize;
                SNode ** m_bucket;
        };
    }
}

#endif
```

```cpp
#include "hashtable.h"
#include <stdlib.h>

#define MAX_BUCKET_SIZE 120000

using namespace JCL::pub;

CHashTable::CHashTable(
    EHashType iHashType,
    int iMaxBucketSize) :
    m_iHashType(iHashType),
    m_iSize(0),
    m_iMaxBucketSize(iMaxBucketSize)
{
    m_iMaxBucketSize = m_iMaxBucketSize > MAX_BUCKET_SIZE ? MAX_BUCKET_SIZE : m_iMaxBucketSize;
    m_bucket = new SNode*[m_iMaxBucketSize];
    for (int i = 0; i < m_iMaxBucketSize; i++) {
        m_bucket[i] = NULL;
    }
}

CHashTable::~CHashTable()
{
    SNode * pNode = NULL;
    for (int i = 0; i < m_iMaxBucketSize; i++)
    {
        pNode = m_bucket[ i ];
        if(NULL != pNode) {
            delete pNode;
        }
    }
    delete m_bucket;
}

void
CHashTable::m_vShowStatus()
{
    printf("\n******************************\n");
    printf("m_iMaxBucketSize: %d\n", m_iMaxBucketSize);
    printf("m_iSize: %d\n", m_iSize);
    int iEmpty = 0;
    int iOne = 0;
    int iTwo = 0;
    int iOthers = 0;
    for (int i = 0; i < m_iMaxBucketSize; i++)
    {
        SNode * pNode = m_bucket[i];
        if(NULL == pNode)
        {
            iEmpty++;
        }
        else
        {
            int iCurNum = 0;
            SNode * pCurNode = pNode;
            while (pCurNode != NULL) {
                iCurNum++;
                pCurNode = pCurNode->m_pNext;
            }

            switch(iCurNum)
            {
            case 1:
                iOne++;
                break;
            case 2:
                iTwo++;
                break;
            default:
                iOthers++;
                break;
            }
        }
    }
    printf("iOne: %d\n", iOne);
    printf("iTwo: %d\n", iTwo);
    printf("iOthers: %d\n", iOthers);
    printf("******************************\n");
}

int
CHashTable::m_iGetSize()
{
    return m_iSize;
}

int
CHashTable::m_iInsert(const char * pcKey, void * pvVal)
{
    if(NULL == pcKey || strlen(pcKey) == 0) {
        printf("m_iInsert() invalid parameters\n");
        return -1;
    }

    unsigned int uiKey = m_iHashFunction(pcKey);
    SNode * pNode = m_bucket[ uiKey % m_iMaxBucketSize ];
    if(NULL == pNode) {
        pNode = new SNode();
        pNode->m_pNext = NULL;
        strcpy(pNode->m_acKey, pcKey);
        pNode->m_pvVal = pvVal;

        m_bucket[ uiKey % m_iMaxBucketSize ] = pNode;
        m_iSize++;
        return 0;
    }

    SNode * pCurNode = pNode;
    while (pCurNode != NULL)
    {
        if(strcmp(pCurNode->m_acKey, pcKey) == 0)
        {
            printf("already exists pcKey=%s\n", pcKey);
            return -1;
        }
        pCurNode = pCurNode->m_pNext;
    }

    SNode * pNewNode = new SNode();
    strcpy(pNewNode->m_acKey, pcKey);
    pNewNode->m_pvVal = pvVal;

    pNewNode->m_pNext = pNode->m_pNext;
    pNode->m_pNext = pNewNode;
    m_iSize++;
    return 0;
}

int
CHashTable::m_iDelete(const char * pcKey)
{
    if(NULL == pcKey || strlen(pcKey) == 0) {
        printf("m_iDelete invalid parameters\n");
        return -1;
    }

    unsigned int uiKey = m_iHashFunction(pcKey);

    SNode * pNode = m_bucket[ uiKey % m_iMaxBucketSize ];
    if(NULL == pNode){
        printf("Not exist in hashtable\n");
        return -1;
    }

    if(strcmp(pNode->m_acKey, pcKey) == 0) {
        m_bucket[ uiKey % m_iMaxBucketSize ] = pNode->m_pNext;
        delete pNode;
        m_iSize--;
        return 0;
    }

    SNode * p1 = pNode->m_pNext;
    SNode * p2 = pNode;
    while (p1 != NULL)
    {
        if(strcmp(p1->m_acKey, pcKey) == 0)
        {
            p2->m_pNext = p1->m_pNext;
            delete p1;
            m_iSize--;
            return 0;
        }

        p1 = p1->m_pNext;
        p2 = p2->m_pNext;
    }

    return -1;
}

void *
CHashTable::m_pvFind(const char * pcKey)
{
    if(NULL == pcKey) {
        return NULL;
    }

    SNode * pNode = m_pstFindNode(pcKey);
    if(NULL == pNode) {
        return NULL;
    }

    return pNode->m_pvVal;
}

SNode *
CHashTable::m_pstFindNode(const char * pcKey)
{
    unsigned int uiKey = m_iHashFunction(pcKey);
    SNode * pNode = m_bucket[ uiKey % m_iMaxBucketSize ];
    if(NULL == pNode) {
        return NULL;
    }

    while (pNode != NULL)
    {
        if(strcmp(pNode->m_acKey, pcKey) == 0) {
            return pNode;
        }
        pNode = pNode->m_pNext;
    }

    return NULL;
}

unsigned int
CHashTable::m_iHashFunction(const char * pcKey)
{
    switch(m_iHashType)
    {
    case EDJBHASH:
        return DJBHash(pcKey);
        break;
    case EAPHASH:
        return APHash(pcKey);
        break;
    default:
        printf("m_iHashFunction() unknown hash type\n");
        break;
    }
    return 0;
}

unsigned int
CHashTable::DJBHash(const char * str)
{
    char * pcKey = const_cast<char*>(str);
    unsigned int hash = 5381;
    while (*pcKey)
    {
        hash = ((hash << 5) + hash) + (*pcKey);
        pcKey++;
    }
    return hash;
}

unsigned int
CHashTable::APHash(const char* str)
{
    char * pcKey = const_cast<char*>(str);

    unsigned int hash = 0xAAAAAAAA;
    unsigned int i    = 0;

    while (*pcKey)
    {
        hash ^= ((i & 1) == 0) ? (  (hash <<  7) ^ (*pcKey) * (hash >> 3)) :
            (~((hash << 11) + ((*pcKey) ^ (hash >> 5))));
        pcKey++;
        i++;
    }

    return hash;
}
```

hash 算法是从下面这个链接复制来的。

[http://www.partow.net/programming/hashfunctions/](http://www.partow.net/programming/hashfunctions/)
