---
layout: post
title:  "[链表] 如何找到一个链表的中间元素"
date:   2016-04-11
comments: true
categories: Art
tags: [C,List]
description:
published: true
---

设置两个指针fast和slow，slow一次走一下，fast一次走两下，这样当fast走到链表的尾部的时候，slow应该正好走到链表的中间。

```cpp
PList pGetMidPoint(PList pList)
{
    PList fast = pList;
    PList slow = pList;
    while (fast!=NULL && fast->next!=NULL) {
        fast = fast->next->next;
        slow = slow->next;
    }
    return slow;
}
```
