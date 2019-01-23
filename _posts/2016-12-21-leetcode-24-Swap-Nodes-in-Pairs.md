---
layout: post
title: "[Art][leetcode - C++] 24 Swap Nodes in Pairs"
date: 2016-12-21
author: mdgsf
comments: true
categories: Art
tags: [Leetcode]
description:
published: true #default true
---

[https://leetcode.com/problems/swap-nodes-in-pairs/description/](https://leetcode.com/problems/swap-nodes-in-pairs/description/)

## 题目

Given a linked list, swap every two adjacent nodes and return its head.

For example,

Given 1->2->3->4, you should return the list as 2->1->4->3.

Your algorithm should use only constant space. You may not modify the values in the list, only nodes itself can be changed.

## 题目翻译

## 题目解析

## 参考答案

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     struct ListNode *next;
 * };
 */
struct ListNode* swapPairs(struct ListNode* head) {
    if (NULL == head || NULL == head->next) {
        return head;
    }
    
    struct ListNode * pCur;
    struct ListNode * pNext;
    struct ListNode * pLastTail;
    
    pLastTail = head;
    pCur = head;
    pNext = pCur->next;
    while (pCur != NULL && pNext != NULL)
    {
        pCur->next = pNext->next;
        pNext->next = pCur;
        
        if(pCur == head)
        {
            head = pNext;
        }
        else
        {
            pLastTail->next = pNext;
        }

        pLastTail = pCur;
        pCur = pCur->next;
        pNext = pCur != NULL ? pCur->next : NULL;
    }
    
    return head;
}
```
