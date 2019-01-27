---
layout: post
title: "[Art][leetcode - C++] 237 Delete Node in a Linked List"
date: 2016-12-03
author: mdgsf
comments: true
categories: Art
tags: [Leetcode]
description:
published: true #default true
---

[https://leetcode.com/problems/delete-node-in-a-linked-list/description/](https://leetcode.com/problems/delete-node-in-a-linked-list/description/)

## 题目

Write a function to delete a node (except the tail) in a singly linked list, given only access to that node.

Supposed the linked list is 1 -> 2 -> 3 -> 4 and you are given the third node with value 3, 
the linked list should become 1 -> 2 -> 4 after calling your function.

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
void deleteNode(struct ListNode* node) {
    if(NULL == node) {
        return;
    }
    
    struct ListNode * pNextNode = node->next;
    node->val = pNextNode->val;
    node->next = pNextNode->next;
    free(pNextNode);
}
```

这样删除节点是有问题的，如果这个节点是最后节点。

就算删除了，那这个节点的前一个节点的next指针也没有置为NULL。
