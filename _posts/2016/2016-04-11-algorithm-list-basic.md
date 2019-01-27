---
layout: post
title:  "[链表] 链表基础算法"
date:   2016-04-11
comments: true
categories: Art
tags: [C,List]
description:
published: true
---

```cpp
#include <stdio.h>
#include <iostream>
using namespace std;

int iAbs(int a)
{
    int i = a >> 31;
    return ((a^i) - i);
}

typedef struct _SListNode {
    int m_iVal;
    void *m_pData;
    _SListNode *next;
    _SListNode() : m_iVal(0), m_pData(NULL), next(NULL) {}
    _SListNode(int iVal) : m_iVal(iVal), m_pData(NULL), next(NULL) {}
}SListNode, *PList;

void vCreateList(PList * ppList)
{
    SListNode * node1 = new SListNode(1);
    SListNode * node2 = new SListNode(2);
    SListNode * node3 = new SListNode(3);
    SListNode * node4 = new SListNode(4);
    SListNode * node5 = new SListNode(5);
    SListNode * node6 = new SListNode(6);
    SListNode * node7 = new SListNode(7);

    *ppList = node1;
    node1->next = node2;
    node2->next = node3;
    node3->next = node4;
    node4->next = node5;
    node5->next = node6;
    node6->next = node7;
}

void vCreateLoopList(PList * ppList)
{
    SListNode * node1 = new SListNode(1);
    SListNode * node2 = new SListNode(2);
    SListNode * node3 = new SListNode(3);
    SListNode * node4 = new SListNode(4);
    SListNode * node5 = new SListNode(5);
    SListNode * node6 = new SListNode(6);
    SListNode * node7 = new SListNode(7);

    *ppList = node1;
    node1->next = node2;
    node2->next = node3;
    node3->next = node4;
    node4->next = node5;
    node5->next = node6;
    node6->next = node7;
    node7->next = node4;
}

void vCreateIntersectionList(PList * ppList1, PList * ppList2)
{
    SListNode * node1 = new SListNode(1);
    SListNode * node2 = new SListNode(2);
    SListNode * node3 = new SListNode(3);
    SListNode * node4 = new SListNode(4);
    SListNode * node5 = new SListNode(5);
    SListNode * node6 = new SListNode(6);
    SListNode * node7 = new SListNode(7);

    *ppList1 = node1;
    node1->next = node2;
    node2->next = node3;
    node3->next = node4;
    node4->next = node5;
    node5->next = node6;
    node6->next = node7;

    SListNode * node2_1 = new SListNode(1);
    SListNode * node2_2 = new SListNode(2);
    SListNode * node2_3 = new SListNode(3);
    SListNode * node2_4 = new SListNode(4);
    *ppList2 = node2_1;
    node2_1->next = node2_2;
    node2_2->next = node2_3;
    node2_3->next = node2_4;
    node2_4->next = node4;

}

void vPrintList(PList pList)
{
    PList pHead = pList;
    while (pHead != NULL) {
        printf("%d ", pHead->m_iVal);
        pHead = pHead->next;
    }
    printf("\n");
}

PList pGetMidPoint(PList pList)
{
    PList fast = pList;
    PList slow = pList;
    while (fast!=NULL && fast->next!=NULL) {
        fast = fast->next->next;
        slow = slow->next;
    }
    return slow;
}

PList pReverseList(PList head)
{
    PList p1 = NULL;
    PList p2 = head;
    PList p3 = NULL;

    while (p2 != NULL)
    {
        p3 = p2->next;
        p2->next = p1;
        p1 = p2;
        p2 = p3;
    }

    return p1;
}

/*
 *  if list has loop, return loop port.
 *  else return NULL.
 */
PList FindLoopPort(PList head)
{
    PList fast = head;
    PList slow = head;

    while (fast != NULL && fast->next != NULL) {
        fast = fast->next->next;
        slow = slow->next;
        if(slow == fast) {
            break;
        }
    }

    if(fast == NULL || fast->next == NULL) {
        return NULL;
    }

    slow = head;
    while (slow != fast) {
        slow = slow->next;
        fast = fast->next;
    }

    return slow;
}

/*
 *  if head1, head2 has intersection, return intersection point,
 *  else return NULL.
 */
PList pFindIntersectionPoint(PList head1, PList head2)
{
    if(NULL == head1 || NULL == head2) {
        return NULL;
    }

    PList p1 = head1;
    PList p2 = head2;
    int iLen1 = 0;
    int iLen2 = 0;
    int iDiffLen = 0;

    while (p1 != NULL) {
        iLen1++;
        p1 = p1->next;
    }
    while (p2 != NULL) {
        iLen2++;
        p2 = p2->next;
    }

    if(p1 != p2) { //last point is different.
        return NULL;
    }

    if(iLen1 > iLen2) {
        p1 = head1;
        p2 = head2;
    } else {
        p1 = head2;
        p2 = head1;
    }

    iDiffLen = iAbs(iLen1 - iLen2);
    for (int i = 0; i < iDiffLen; i++) {
        p1 = p1->next;
    }

    while (p1 != p2) {
        p1 = p1->next;
        p2 = p2->next;
    }

    return p1;
}

int main()
{
    printf("list demo\n");

    PList pList = NULL;
    vCreateList(&pList);
    vPrintList(pList);

    PList pMid = pGetMidPoint(pList);
    printf("mid point: %d\n", pMid->m_iVal);

    pList = pReverseList(pList);
    vPrintList(pList);

    pList = pReverseList(pList);
    vPrintList(pList);

    PList pLoopPoint = FindLoopPort(pList);
    if(pLoopPoint == NULL) {
        printf("list has no loop\n");
    }

    PList pLoopList = NULL;
    vCreateLoopList(&pLoopList);
    pLoopPoint = FindLoopPort(pLoopList);
    if(pLoopPoint == NULL) {
        printf("pLoopList has no loop\n");
    } else {
        printf("pLoopList has loop\n");
    }




    PList pList2 = NULL;
    vCreateList(&pList2);

    PList pIntersectionPoint = pFindIntersectionPoint(pList, pList2);
    if(pIntersectionPoint == NULL) {
        printf("pList pList2 has no intersection point\n");
    }


    PList pList3 = NULL;
    PList pList4 = NULL;
    vCreateIntersectionList(&pList3, &pList4);
    pIntersectionPoint = pFindIntersectionPoint(pList3, pList4);
    if(pIntersectionPoint == NULL) {
        printf("pList3 pList4 has no intersection point\n");
    } else {
        printf("pList3 pList4 has intersection point: %d\n",
                pIntersectionPoint->m_iVal);
    }

    return 0;
}
```
