---
layout: post
title: "[Art][leetcode] 2 Add Two Numbers"
date: 2017-07-19
author: mdgsf
comments: true
categories: Art
tags: [Leetcode]
description:
published: true #default true
---

[https://leetcode.com/problems/add-two-numbers/](https://leetcode.com/problems/add-two-numbers/)

## 题目

You are given two non-empty linked lists representing two non-negative integers. The digits are stored in reverse order and each of their nodes contain a single digit. Add the two numbers and return it as a linked list.

You may assume the two numbers do not contain any leading zero, except the number 0 itself.

Input: (2 -> 4 -> 3) + (5 -> 6 -> 4)
Output: 7 -> 0 -> 8

## 题目翻译

## 题目解析

## 参考答案

```cpp
#include <iostream>
#include <stdio.h>
#include <stdlib.h>

using namespace std;

struct ListNode
{
    int val;
    ListNode *next;
    ListNode(int x) : val(x), next(NULL) {}
};

// Input: (2 -> 4 -> 3) + (5 -> 6 -> 4)
// Output: 7 -> 0 -> 8

class Solution
{
  public:
    ListNode *addTwoNumbers(ListNode *l1, ListNode *l2)
    {
        ListNode * pHead = NULL;
        ListNode * pTail = NULL;


        int iFlag = 0;
        while (l1 != NULL && l2 != NULL)
        {
            int iNum = l1->val + l2->val;
            iNum += iFlag;

            if (iNum >= 10)
            {
                iNum -= 10;
                iFlag = 1;
            }
            else
            {
                iFlag = 0;
            }

            ListNode * pNewNode = new ListNode(iNum);
            if(pHead == NULL)
            {
                pHead = pNewNode;
                pTail = pNewNode;
            }
            else
            {
                pTail->next = pNewNode;
                pTail = pNewNode;
            }

            l1 = l1->next;
            l2 = l2->next;
        }

        while(l1 != NULL)
        {
            int iNum = l1->val;
            iNum += iFlag;

            if (iNum >= 10)
            {
                iNum -= 10;
                iFlag = 1;
            }
            else
            {
                iFlag = 0;
            }

            ListNode * pNewNode = new ListNode(iNum);
            if(pHead == NULL)
            {
                pHead = pNewNode;
                pTail = pNewNode;
            }
            else
            {
                pTail->next = pNewNode;
                pTail = pNewNode;
            }

            l1 = l1->next;
        }

        while(l2 != NULL)
        {
            int iNum = l2->val;
            iNum += iFlag;

            if (iNum >= 10)
            {
                iNum -= 10;
                iFlag = 1;
            }
            else
            {
                iFlag = 0;
            }

            ListNode * pNewNode = new ListNode(iNum);
            if(pHead == NULL)
            {
                pHead = pNewNode;
                pTail = pNewNode;
            }
            else
            {
                pTail->next = pNewNode;
                pTail = pNewNode;
            }

            l2 = l2->next;
        }

        if(iFlag == 1)
        {
            ListNode * pNewNode = new ListNode(1);
            pTail->next = pNewNode;
            pTail = pNewNode;
        }

        return pHead;
    }
};

int main()
{
    return 0;
}
```
