---
layout: post
title:  "[C Primer Plus] 复习笔记--第7章"
date:   2016-12-05
comments: true
categories: CPrimerPlus
tags: [Cprimerplus]
description:
published: true
---

### 1

常用的字符函数

```
isalnum() 字母或数字
isalpha() 字母
isblank() 空白字符
isspace() 空白字符
isdigit() 数字
islower() 小写字母
isupper() 大写字母
isprint() 可打印字符
isgraph() 除空格之外的所有可打印字符
ispunct() 标点符号

tolower()
toupper()
```

### 2

一个 if/else 的 bug

```cpp
if(number > 6)
    if(number < 12)
        printf("You're close!\n");
else
    printf("Sorry, you lose a turn\n");
```

这个else是和 if(number < 12)配对的。

else和它最近的一个if相匹配。

**TIPS**：只要所有的if和else都加上括号，就永远不会有这种bug。

### 3

判素数

```cpp
bool bIsPrimer(int num)
{
    int div = 2;
    bool isPrime = true;
    for ( ; (div*div) <= num; div++)
    {
        if(num % div == 0)
        {
            isPrime = false;
            break;
        }
    }
    return isPrime;
}
```

### 4

```
&& 的优先级高于 ||

a > b && b > c || b > d
等价于 ((a>b) && (b>c)) || (b>d)
```

### 5

C语言保证逻辑表达式是从左到右求值的。 && 和 || 运算符是序列的分界点。
因此在程序从一个操作数前进到下一个操作数之前，所有的副作用都会生效。

```cpp
while ((c = getchar()) != ' ' && c != '\n')
```

```cpp
while (x++ < 10 && x+y < 20)
```

&&运算符是序列的分界点。保证了在对右边表达式求值之前，先把x的值增加1.

### 6

switch 的判断表达式应具有整数值（包括char类型）。

case 标签必须是 整形常量 或 整数常量表达式。

case 标签不能使用变量。
