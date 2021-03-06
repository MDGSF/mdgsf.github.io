---
layout: post
title:  "[C Primer Plus] 复习笔记--第5章"
date:   2016-12-03
comments: true
categories: CPrimerPlus
tags: [Cprimerplus]
description:
published: true
---

### 1

几个常见的运算符的优先级

```
()    >   +-(一元运算符)  >  */    >    +-(二元运算符)  >  =
括号        正负             乘除         加减             等号
```

### 2

sizeof 运算符是以字节为单位返回其操作数的大小。

sizeof不是函数

### 3

typedef double real;

使real成为double的别名。

### 4

取模运算符: %  只用于整数。

不要对浮点数使用该运算符，那将是无效的。

a % b = a - (a/b)*b   这个式子只要a和b是整数就成立，不论正负。

例子：

```
11 % 5 = 11 - (11/5)*5 = 1
11 % -1 = 11 - (11/-2)*(-2) = 1
-11 % -5 = -11 - (-11/-5)*(-5) = -1
-11 % 5 = -11 - (-11/5)*5 = -1
```

### 5

```
++ -- 增量运算符，减量运算符

通常会产生更高效的机器语言代码。
```

### 6

```
x*y++  代表  (x)*(y++)
```

增量和减量运算符有很高的优先级，只有圆括号比他们的优先级高。

增量和减量运算符只能对变量起作用， 而 (x*y) 不是一个变量。

### 7

n++ 先使用n，然后将它的值增加。

++n 先将n的值增加，然后再使用它。

### 8

如果一个变量出现在同一个函数的多个参数中时，不要将增量或减量运算符用于它上面。

当一个变量多次出现在一个表达式里时，不要将增量或减量运算符遇于它上面。

### 9

顺序点

定义：一个顺序点是程序执行中的一点：在该点处，所有的副作用都在进入下一步前被计算。

例如：

语句里的分号标志了一个顺序点。

任何一个完整的表达式的结束也是一个顺序点。

**一个完整的表达式**：它不是一个更大的表达式的子表达式。

**例子1**：

```cpp
while (guests++ < 10)
    printf("%d \n", guests);
```

因为 guests++ < 10 是一个完整的表达式，C语言保证副作用进入 printf 之前发生。

**例子2**：

y = (4 + x++) + (6 + x++);

表达式　4 + x++　不是一个完整的表达式，所以C语言不保证在计算子表达式　4 + x++　之后立即增加Ｘ。

完整表达式是整个赋值语句，并且分号标记了顺序点，所以C语言保证在进入后续语句前Ｘ将被增加两次。
