---
layout: post
title:  "[Qt] memset不能对class的实例使用"
date:   2016-03-30
comments: true
categories: Qt
tags: [C++,Qt]
description:
published: true
---

```cpp
typedef struct _SNode {
    QString str;
}SNode;

1. SNode *pNode = (SNode*)malloc(sizeof(SNode));
这是错误的， 有问题， 要用new。
SNode *pNode = new SNode;

2.
SNode node;
memset(&node, 0, sizeof(node));
这也有问题，好像在结构体内有类，类不能使用memset.

3. memcpy 用在这用情况也有问题。
```
