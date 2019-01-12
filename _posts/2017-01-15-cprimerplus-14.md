---
layout: post
title:  "[C Primer Plus] 复习笔记--第14章"
date:   2017-01-15
comments: true
categories: CPrimerPlus
tags: CPrimerPlus
description:
published: true
---

### 1. 结构体初始化

```cpp
struct book {
	char title[MAXTITLE];
	char author[MAXAUTL];
	float value;
} library;

struct book mybook = {
	"The Private and the Pamsel",
	"Renee Vivotte",
	1.95
};
```

如果初始化一个具有静态存储时期的结构，初始化项目列表中的值必须是常量表达式。

如果存储时期是自动的，列表中的值就不必是常量了。


### 2 

&mybook.float

因为点拥有比&更高的优先级，因此这个表达式和 &(mybook.float) 一样。


### 3. 向一个函数传递结构信息

1. 把结构作为参数传递。

2. 把指向结构的指针作为参数传递。

3. 还可以将结构体成员作为参数传递给函数。


### 4. 

C 允许把一个结构赋值给另一个结构，即使有一个成员是数组也一样可以。

只要结构体中没有指针，就可以直接用等号赋值。


