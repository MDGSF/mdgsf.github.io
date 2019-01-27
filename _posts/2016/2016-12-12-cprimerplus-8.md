---
layout: post
title:  "[C Primer Plus] 复习笔记--第8章"
date:   2016-12-12
comments: true
categories: CPrimerPlus
tags: [Cprimerplus]
description:
published: true
---

### 1

输入分为缓冲输入和非缓冲输入。

缓冲输入分为完全缓冲输入和行缓冲输入。

完全缓冲输入：当缓冲区满时被清空。

行缓冲：遇到一个换行字符时将被清空。

键盘输入是标准的行缓冲，因此按下回车键将清空缓冲区。


### 2 

使用标准I/O包时，就会自动屏蔽掉不同系统下的文件差异。

例如：要检查一个换行符，可以使用 if(ch=='\n')

如果该系统实际上使用回车/换行的组合，则I/O函数自动在两种表示法之间转换。


### 3

```cpp
int ch;
while ((ch = getchar()) != EOF)
	putchar(ch);
```


### 4

```
拷贝一份mywords的副本 
mycopy > savewords < mywords  (正确)
mycopy < mywords > savewords  (正确)

mycopy < mywords > mywords  (错误)
这是不对的，原因是 > mywords 使原始的mywords文件在用于输入之前就被截断为空了。
```

**Tips**: 输入不能来自一个以上的文件，输出也不能定向至一个以上的文件。


### 5

```
 >> 向一个现有文件的末尾追加数据。

 | 管道运算符，它可以将一个程序的输出与第二个程序的输入连接起来。
```


### 6

```cpp
while (getchar() != '\n')
	continue;
```

### 7

getchar() 和 scanf() 在检测到文件结尾时，都返回 EOF
























