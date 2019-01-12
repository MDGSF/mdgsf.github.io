---
layout: post
title: "[Art] [leetcode - C++] 2 Add Two Numbers"
date: 2016-12-21
author: mdgsf
comments: true
categories: Art
tags: leetcode
description:
published: true #default true
---

[https://leetcode.com/problems/add-two-numbers/description/](https://leetcode.com/problems/add-two-numbers/description/)

## 题目

You are given two linked lists representing two non-negative numbers. 
The digits are stored in reverse order and each of their nodes contain a single digit. 
Add the two numbers and return it as a linked list.

Input: (2 -> 4 -> 3) + (5 -> 6 -> 4)

Output: 7 -> 0 -> 8

## 题目翻译

## 题目解析

## 参考答案

```cpp
/**
 * Definition for singly-linked list.
 * struct ListNode {
 *     int val;
 *     ListNode *next;
 *     ListNode(int x) : val(x), next(NULL) {}
 * };
 */
class Solution {
public:
    ListNode* addTwoNumbers(ListNode* l1, ListNode* l2) {
        ListNode * pRetHead = NULL;
        ListNode * pRetTail = NULL;
        
        while (NULL != l1 && NULL != l2)
        {
            ListNode * pNode = new ListNode(l1->val + l2->val);
            if(NULL == pRetHead) {
                pRetHead = pNode;
                pRetTail = pNode;
            } else {
                pRetTail->next = pNode;
                pRetTail = pNode;
            }
            
            l1 = l1->next;
            l2 = l2->next;
        }
        
        while (NULL != l1) 
        {
            ListNode * pNode = new ListNode(l1->val);
            pRetTail->next = pNode;
            pRetTail = pNode;
            
            l1 = l1->next;
        }
        
        while (NULL != l2) 
        {
            ListNode * pNode = new ListNode(l2->val);
            pRetTail->next = pNode;
            pRetTail = pNode;
            
            l2 = l2->next;
        }
        
        int iFlag = 0;
        ListNode * p = pRetHead;
        while (NULL != p)
        {
            p->val = p->val + iFlag;
            int iNum = p->val / 10;
            if(iNum)
            {
                iFlag = 1;
                p->val = p->val % 10;
            } 
            else
            {
                iFlag = 0;
            }
            
            p = p->next;
        }
        if(iFlag == 1) {
            ListNode * pNode = new ListNode(1);
            pRetTail->next = pNode;
            pRetTail = pNode;
        }
        
        return pRetHead;
    }
};
```
