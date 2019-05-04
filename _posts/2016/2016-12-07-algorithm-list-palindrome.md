---
layout: post
title: "[链表] 判断一个链表是不是回文串"
date: 2016-12-07
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

PSLinkNode getMiddle(PSLinkNode head)
{
    if(NULL == head)
    {
        return NULL;
    }

    PSLinkNode fast = head;
    PSLinkNode slow = head;
    while (fast->next != NULL && fast->next->next != NULL)
    {
        fast = fast->next->next;
        slow = slow->next;
    }
    return slow;
}

PSLinkNode reverseList(PSLinkNode head)
{
    if(NULL == head || head->next == NULL)
    {
        return head;
    }

    PSLinkNode p3 = head;
    PSLinkNode p2 = head->next;
    PSLinkNode p1 = head->next->next;

    p3->next = NULL;
    p2->next = p3;
    while (p1 != NULL)
    {
        p3 = p2;
        p2 = p1;
        p1 = p1->next;

        p2->next = p3;
    }

    return p2;
}

bool bIsPalindrome(PSLinkNode head)
{
    if(NULL == head || head->next == NULL)
    {
        return true;
    }

    PSLinkNode middle = getMiddle(head);

    PSLinkNode right = reverseList(middle->next);

    while (right != NULL)
    {
        if(right->val != head->val)
        {
            return false;
        }

        right = right->next;
        head = head->next;
    }
    return true;
}

void vTest(const char * pcCaseName, int ai[], int num, bool bExpectResult)
{
    PSLinkNode list = createList(ai, num);
    ShowList(list);
    bool bRet = bIsPalindrome(list);
    if(bRet == bExpectResult)
    {
        printf("%s: pass\n", pcCaseName);
    }
    else
    {
        printf("%s: failed\n", pcCaseName);
    }
}

void vTest1()
{
    int ai[] = {1, 2, 3, 2, 1};
    int num = sizeof ai / sizeof ai[0];
    vTest("vTest1", ai, num, true);
}

void vTest2()
{
    int ai[] = {1, 2, 3, 4, 1};
    int num = sizeof ai / sizeof ai[0];
    vTest("vTest2", ai, num, false);
}

void vTest3()
{
    int ai[] = {1, 2, 2, 1};
    int num = sizeof ai / sizeof ai[0];
    vTest("vTest3", ai, num, true);
}

void vTest4()
{
    int ai[] = {1};
    int num = sizeof ai / sizeof ai[0];
    vTest("vTest4", ai, num, true);
}

void vTest5()
{
    int ai[] = {1, 2};
    int num = sizeof ai / sizeof ai[0];
    vTest("vTest5", ai, num, false);
}

int main()
{
    vTest1();
    vTest2();
    vTest3();
    vTest4();
    vTest5();
    return 0;
}
```
