---
layout: post
title:  "[C Primer Plus] 复习笔记--第11章"
date:   2017-01-15
comments: true
categories: CPrimerPlus
tags: [Cprimerplus]
description:
published: true
---

### 1

字符串是以空字符 \0 结尾的char数组。

### 2

如果字符串文字中间没有间隔或者间隔是空格，C语言会将其串联起来。下面两个是等价的。

```cpp
char greeting[50] = "Hello, and " "how are "   "you "  "today!";
char greeting[50] = "Hello, and how are you today!";
```

### 3

字符串常量属于静态存储。静态存储是指如果在一个函数中使用字符串常量，
即使是多次调用了这个函数，该字符串在程序的整个运行过程中只存储一份。
整个引号中的内容作为指向该字符串存储位置的指针。


### 4

const char m1[40] = "Limit yourself";

字符数组名也是数组首元素的地址。

m1 == &m1[0]

*m1 == 'L'

*(m1+1) == m1[1] == 'i'


### 5. 数组与指针

char heart[] = "I Love you";

char * head = "I Love you";

1. heart是个常量，head是个变量。

2. 都是在静态存储区为字符串分配空间，heart表示的就是第一个字节的地址，
而系统还需要为head分配一个指针的存储位置。

3. 数组的初始化是从静态存储区把一个字符串复制给数组，
而指针的初始化只是复制字符串的地址。

### 6. 字符串数组

const char * mytal[5] = {"Adding numbers", "huang jian"};













