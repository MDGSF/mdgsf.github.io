---
layout: post
title: "[链表] 判断链表是否存在环"
date: 2016-12-09
author: mdgsf
comments: true
categories: Art
tags: [C]
description:
published: true #default true
---


```cpp
#include <stdio.h>
#include <stdlib.h>

typedef struct _SLinkNode {
    int val;
    struct _SLinkNode * next;
}SLinkNode, *PSLinkNode;

PSLinkNode NewNode(int val)
{
    PSLinkNode newnode = (PSLinkNode)malloc(sizeof(SLinkNode));
    newnode->val = val;
    newnode->next = NULL;
    return newnode;
}

PSLinkNode createList(int ai[], int num)
{
    PSLinkNode head = NULL;
    PSLinkNode tail = NULL;
    for (int i = 0; i < num; i++)
    {
        PSLinkNode newnode = NewNode(ai[i]);
        if(head == NULL)
        {
            head = newnode;
            tail = newnode;
        }
        else
        {
            tail->next = newnode;
            tail = newnode;
        }
    }
    return head;
}

void ShowList(PSLinkNode head)
{
    while(head != NULL)
    {
        printf("%d ", head->val);
        head = head->next;
    }
    printf("\n");
}

bool bExistsLoop(PSLinkNode head)
{
    PSLinkNode fast = head;
    PSLinkNode slow = head;
    while (fast != NULL && fast->next != NULL)
    {
        fast = fast->next->next;
        slow = slow->next;
        if(fast == slow)
        {
            break;
        }
    }

    return (fast == NULL || fast->next == NULL) ? false : true;
}

PSLinkNode FindLoopPort(PSLinkNode head)
{
    PSLinkNode fast = head;
    PSLinkNode slow = head;
    while (fast != NULL && fast->next != NULL)
    {
        fast = fast->next->next;
        slow = slow->next;
        if(fast == slow)
        {
            break;
        }
    }

    if (fast == NULL || fast->next == NULL) 
    {
        return NULL;
    }

    slow = head;
    while (slow != fast)
    {
        slow = slow->next;
        fast = fast->next;
    }

    return slow;
}

int main()
{
    int ai[] = {1, 2, 3, 4, 5, 6, 7, 8};
    PSLinkNode list = createList(ai, 8);
    ShowList(list);

    PSLinkNode pCur = list;
    while (pCur->next != NULL)
    {
        pCur = pCur->next;
    }
    pCur->next = list->next;

    printf("%d\n", bExistsLoop(list));

    PSLinkNode pLoopPort = FindLoopPort(list);
    printf("%d\n", pLoopPort->val);

    return 0;
}
```




