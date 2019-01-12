---
layout: post
title:  "[python] Baisc usage"
date:   2017-08-30
comments: true
categories: python
tags: python 
description:
published: true
---


## 1 基础运算

```python
>>> 2 ** 3   #2^3
8
>>> 22 % 8
6
>>> 22 / 8
2.75
>>> 22 // 8
2
```


## 2 字符串连接

```python
>>> 'Alice' + 'Bob'
'AliceBob'
```

## 3

```python
>>> 'Alice' * 5
'AliceAliceAliceAliceAlice'
```


## 4 hello.py

```python
# comments
print('Hello world!')
print('What is your name?')
myName = input()
print('It is good to meet you, ' + myName)
print('The length of your name is:')
print(len(myName))
print('What is your age?')
myAge = input()
print('You will be ' + str(int(myAge) + 1) + ' in a year.')
```


## 5 str() int() float()

```python
>>> str(0)
'0'
>>> str(-3.14)
'-3.14'
>>> int('42')
42
>>> int('-99')
-99
>>> int(1.25)
1
>>> float('3.14')
3.14
>>> float(10)
10.0
>>> int('3.14')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ValueError: invalid literal for int() with base 10: '3.14'
```


## 6 round()


round(...)
    round(number[, ndigits]) -> number

    Round a number to a given precision in decimal digits (default 0 digits).
    This returns an int when called with one argument, otherwise the
    same type as the number. ndigits may be negative.

```python
>>> round(10)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
KeyboardInterrupt
>>> round(10.)
10
>>> round(10.0)
10
>>> round(10.1)
10
>>> round(10.123)
10
>>> round(10.123,1)
10.1
>>> round(10.123,2)
10.12
>>> round(10.123,3)
10.123
```
