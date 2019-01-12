---
layout: post
title:  "[python] bubble sort"
date:   2017-08-28
comments: true
categories: python
tags: python, bubble sort
description:
published: true
---


```python
"""
This is bubble
"""

#from __future__ import print_function

def bubble_sort(collection):
    length = len(collection)
    for i in range(length - 1, -1, -1):
        for j in range(i):
            if collection[j] > collection[j+1]:
                collection[j], collection[j+1] = collection[j+1], collection[j]

    return collection


def bubble_while(collection):
    i = len(collection) - 1
    while i >=0 :
        j = 0
        while j < i - 1:
            if collection[j] > collection[j+1]:
                collection[j], collection[j+1] = collection[j+1], collection[j]
            j = j + 1
        i = i - 1
    
    return collection


L = [2,3,1,3,5,542,5,45,3,3]
print(bubble_sort(L))
print(bubble_while(L))
```