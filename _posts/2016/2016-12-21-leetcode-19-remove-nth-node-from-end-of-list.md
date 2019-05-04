---
layout: post
title: "[Art][leetcode] 19 Remove Nth Node From End of List"
date: 2016-12-21
author: mdgsf
comments: true
categories: Art
tags: [Leetcode]
description:
published: true #default true
---

[https://leetcode.com/problems/remove-nth-node-from-end-of-list/description/](https://leetcode.com/problems/remove-nth-node-from-end-of-list/description/)

## 题目

Given a linked list, remove the nth node from the end of list and return its head.

For example,

```
   Given linked list: 1->2->3->4->5, and n = 2.

   After removing the second node from the end, the linked list becomes 1->2->3->5.
```

**Note:**

Given n will always be valid.

Try to do this in one pass.

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
    ListNode *removeNthFromEnd(ListNode *head, int n) {
        int iLen = 0;
        ListNode * p = head;
        while(p != NULL)
        {
            iLen++;
            p = p->next;
        }

        p = head;
        for (int i=0; i<iLen-n-1; i++)
        {
            p = p->next;
        }

        if((p == head) && (n == iLen))
        {
            head = p->next;
            delete p;
            return head;
        }

        ListNode * pstDel = p->next;
        if(pstDel->next == NULL)
        {
            p->next = NULL;
        }
        else
        {
            p->next = pstDel->next;
        }
        delete pstDel;
        return head;
    }
};
```

这个方法遍历的2遍。

如果要遍历1遍，则需要拿一个指针p1指向head，p1先向前走n步，
然后再拿一个指针p2指向head，p1和p2同时向前，p1走到尾部时，p2就指向了要删除的节点。
