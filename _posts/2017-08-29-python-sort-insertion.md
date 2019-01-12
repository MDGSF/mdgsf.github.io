---
layout: post
title:  "[python] Insertion sort"
date:   2017-08-29
comments: true
categories: python
tags: python, insertion sort
description:
published: true
---


```python

def insertion_sort(collection):
    
    for index in range(1, len(collection)):
        while 0 < index and collection[index] < collection[index-1]:
            collection[index],collection[index-1] = collection[index-1],collection[index]
            index -= 1

    return collection

L = [7,1,2,3,9,4,10,5,6,8]
print(insertion_sort(L))

```
