---
layout: post
title:  "[python] for and range()"
date:   2017-08-30
comments: true
categories: python
tags: python 
description:
published: true
---

## 1

```python
print('My name is')
for i in range(5):
    print('Jimmy Five Times (' + str(i) + ')')
```

```
My name is
Jimmy Five Times (0)
Jimmy Five Times (1)
Jimmy Five Times (2)
Jimmy Five Times (3)
Jimmy Five Times (4)
```

## 2

```python
for i in range(12, 16):
    print(i)
```

```
12
13
14
15
```

## 3

```python
for i in range(0, 10, 2):
    print(i)
```

```
0
2
4
6
8
```

## 4

```python
for i in range(5, -1, -1):
    print(i)
```

```
5
4
3
2
1
0
```




