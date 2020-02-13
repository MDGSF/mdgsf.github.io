---
layout: post
title: "[算法学习][leetcode] 21 Merge Two Sorted Lists"
date: 2016-12-21
author: mdgsf
comments: true
categories: Art
tags: [Leetcode]
description:
published: true #default true
---

[https://leetcode.com/problems/merge-two-sorted-lists/description/](https://leetcode.com/problems/merge-two-sorted-lists/description/)

## 题目

Merge two sorted linked lists and return it as a new list.
The new list should be made by splicing together the nodes of the first two lists.

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
struct ListNode* mergeTwoLists(struct ListNode* l1, struct ListNode* l2) {
    if(NULL == l1 && NULL == l2) {
        return NULL;
    }
    if(NULL == l1) {
        return l2;
    }
    if(NULL == l2) {
        return l1;
    }

    struct ListNode stDump;
    struct ListNode * pNewHead;
    pNewHead = &stDump;
    while (l1 != NULL && l2 != NULL)
    {
        if(l1->val < l2->val)
        {
            pNewHead->next = l1;
            l1 = l1->next;
        }
        else
        {
            pNewHead->next = l2;
            l2 = l2->next;
        }
        pNewHead = pNewHead->next;
    }


    if (l1 != NULL)
    {
        pNewHead->next = l1;
    }

    if(l2 != NULL)
    {
        pNewHead->next = l2;
    }

    return stDump.next;
}
```
