---
layout: post
title: "[Art][leetcode - C++] 404 Sum of Left leaves"
date: 2016-09-25
author: mdgsf
comments: true
categories: Art
tags: [Leetcode]
description:
published: true #default true
---

[https://leetcode.com/problems/sum-of-left-leaves/description/](https://leetcode.com/problems/sum-of-left-leaves/description/)

## 题目

Find the sum of all left leaves in a given binary tree.

Example: 

```
    3
   / \
  9  20
    /  \
   15   7

There are two left leaves in the binary tree, with values 9 and 15 respectively. Return 24.
```

## 题目翻译

## 题目解析

## 参考答案

```cpp

/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode(int x) : val(x), left(NULL), right(NULL) {}
 * };
 */
class Solution {
public:
    int sumOfLeftLeaves(TreeNode* root) {
        if(NULL == root)
        {
            return 0;
        }
        
        int iSum = 0;
        if(root->left != NULL && (root->left->left == NULL && root->left->right == NULL))
        {
            iSum = root->left->val;
        }
        
        return iSum + sumOfLeftLeaves(root->left) + sumOfLeftLeaves(root->right);
    }
    
};

```
