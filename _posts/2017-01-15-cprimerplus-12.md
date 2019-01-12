---
layout: post
title:  "[C Primer Plus] 复习笔记--第12章"
date:   2017-01-15
comments: true
categories: CPrimerPlus
tags: CPrimerPlus
description:
published: true
---

### 1. 作用域

1. 代码块作用域：在代码中定义的变量具有代码块作用域。

2. 文件作用域：一个在所有函数之外定义的变量具有文件作用域。

3. 函数原型作用域：

4. 函数作用域：


### 2. 链接

1. 外部链接(external linkage):

2. 内部链接(internal linkage):

3. 空链接(no linkage): 具有代码块作用域或者函数原型作用域的变量有空链接。

```cpp
int giants = 5;   //文件作用域，外部链接
static int dodgers = 3;   //文件作用域，内部链接
int main()
{
}
```

### 3. 存储时期

1. 静态存储时期：如果一个变量具有静态存储时期，它在程序执行期间将一直存在。

2. 自动存储时期：


### 4

存储类             | 时期 | 作用域 | 链接 | 声明方式
自动               | 自动 | 代码块 | 空   | 代码块内
寄存器             | 自动 | 代码块 | 空   | 代码块内，只用关键字register
具有外部链接的静态 | 静态 | 文件   | 外部 | 所有函数之外
具有内部链接的静态 | 静态 | 文件   | 内部 | 所有函数之外，使用关键字static
空连接的静态       | 静态 | 代码块 | 空   | 代码块内，使用关键字static


### 5

自动变量不会自动初始化，

```cpp
int main()
{
	int repid;
	int tents = 5;
}
```

tents 初始化为5， 而repid是任意值。


### 6

寄存器变量多是存放在一个寄存器而非内存中，所以无法获得寄存器变量的地址。

register 声明一个寄存器变量仅是一个请求，而非一条直接命令。
所以，变量有可能只是一个普通的自动变量，然而，您依然不能对它使用
地址运算符。

### 7

1. 外部变量会自动初始化为零。

2. 只可以使用常量表达式来初始化文件作用域变量。


### 8

int tern = 1;  //定义声明 

extern int tern;  //引用声明


### 9

const float * pf;  //指向一个常量浮点数值

float const * pf;  //指向一个常量浮点数值

float * const pf; //pf必须总是指向同一个地址。


### 10. 如何使用全局数据

#### 10.1 方法一

```cpp
/* file1.c */
const double PI = 3.141592;


/* file2.c */
extern const double PI;
```

#### 10.2 方法二

```cpp
/* constant.h */
static const double PI = 3.141592;  //不加static会出现重定义

/* file1.c */
#include "constant.h"

/* file2.c */
#include "constant.h"
```

#### 10.3 方法三

```cpp
/* constant.c */
#include "constant.h"
const double PI = 3.141592;

/* constant.h */
extern const double PI;


/* file1.c */
#include "constant.h"


/* file2.c */
#include "constant.h"
```



