---
layout: post
title:  "[python] range"
date:   2017-08-28
comments: true
categories: python
tags: python, range
description:
published: true
---


range(stop) -> range object

range(start, stop[, step]) -> range object

Return a sequence of numbers from start to stop by step.


```python
>>> list(range(10))
[0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
>>> list(range(2,10))
[2, 3, 4, 5, 6, 7, 8, 9]
>>> list(range(2, 10, 2))
[2, 4, 6, 8]
>>> list(range(10, 2))
[]
>>> list(range(10, 2, -1))
[10, 9, 8, 7, 6, 5, 4, 3]
>>> list(range(10, -1, -1))
[10, 9, 8, 7, 6, 5, 4, 3, 2, 1, 0]
>>> list(range(10, -1, -2))
[10, 8, 6, 4, 2, 0]
``` 


