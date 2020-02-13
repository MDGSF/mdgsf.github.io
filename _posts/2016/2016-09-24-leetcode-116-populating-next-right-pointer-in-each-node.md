---
layout: post
title: "[算法学习][leetcode] 116 Populating Next Right Pointers in Each Node"
date: 2016-09-24
author: mdgsf
comments: true
categories: Art
tags: [Leetcode]
description:
published: true #default true
---

[https://leetcode.com/problems/populating-next-right-pointers-in-each-node/description/](https://leetcode.com/problems/populating-next-right-pointers-in-each-node/description/)

## 题目

Given a binary tree

```
struct TreeLinkNode {
  TreeLinkNode *left;
  TreeLinkNode *right;
  TreeLinkNode *next;
}
```

Populate each next pointer to point to its next right node. If there is no next right node, the next pointer should be set to NULL.

Initially, all next pointers are set to NULL.

**Note:**

- You may only use constant extra space.
- You may assume that it is a perfect binary tree (ie, all leaves are at the same level, and every parent has two children).

For example,

Given the following perfect binary tree,

```
         1
       /  \
      2    3
     / \  / \
    4  5  6  7
```

After calling your function, the tree should look like:

```
         1 -> NULL
       /  \
      2 -> 3 -> NULL
     / \  / \
    4->5->6->7 -> NULL
```

## 题目翻译

## 题目解析

## 参考答案

```cpp

/**
 * Definition for binary tree with next pointer.
 * struct TreeLinkNode {
 *  int val;
 *  TreeLinkNode *left, *right, *next;
 *  TreeLinkNode(int x) : val(x), left(NULL), right(NULL), next(NULL) {}
 * };
 */
class Solution {
public:
    void connect(TreeLinkNode *root) {
        if(NULL == root) {
            return ;
        }

        queue<TreeLinkNode *> nodeQueue;
        nodeQueue.push(root);
        m_vConnect(nodeQueue);
    }

    void m_vConnect(queue<TreeLinkNode *> & nodeQueue)
    {
        if(nodeQueue.empty()) {
            return ;
        }

        TreeLinkNode * pTail = NULL;
        int iCurSize = nodeQueue.size();
        for (int i = 0; i < iCurSize; i++)
        {
            TreeLinkNode * pNode = nodeQueue.front();
            nodeQueue.pop();
            if(pNode->left != NULL)
            {
                nodeQueue.push(pNode->left);
                nodeQueue.push(pNode->right);
            }

            if(i == 0)
            {
                pTail = pNode;
            }
            else
            {
                pTail->next = pNode;
                pTail = pNode;
            }

        }

        m_vConnect(nodeQueue);
    }
};

```
