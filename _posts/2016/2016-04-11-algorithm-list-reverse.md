---
layout: post
title:  "[链表] 链表反转"
date:   2016-04-11
comments: true
categories: Art
tags: [C,List]
description:
published: true
---

```cpp
PList pReverseList(PList head)
{
    PList p1 = NULL;
    PList p2 = head;
    PList p3 = NULL;

    while (p2 != NULL)
    {
        p3 = p2->next;
        p2->next = p1;
        p1 = p2;
        p2 = p3;
    }

    return p1;
}
```
