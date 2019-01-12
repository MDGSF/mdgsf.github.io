---
layout: post
title:  "[C++] 记录一个C++内存bug"
date:   2017-12-03
comments: true
categories: c
tags: memory bug
description:
published: true
---

事情是这样的，前两天同事找到我帮忙看个bug。

在一个C++的类中，增加了一个新的成员函数funcA，结果程序就Crash掉。但是诡异的地方就在于Crash的地方并不在funcA这个函数中，而是在另外一个函数中的一个成员变量memB(memB是一个结构体)，可新增的funcA这个函数完全没有修改过memB这个成员变量。

**思路：**

其实这非常明显就是内存越界了，在funcA修改内存的时候，越界了，修改到了memB的内存了。

那么为什么funcA修改的内存会影响到memB呢？那就要简单说下C++的内存了。

```C++
class Student
{
    funcA()
    funcB()

private:
    int memA;
    int memB;
    int memC;
};
```

可以看下上面这个伪代码，在这个类中，memA，memB，memC中的内存都是并排的。

所以如果在funcA中修改了memA的内容，结果由于代码bug导致内存越界了，就有可能修改到memB，这样就会出现明明没有动过memB，程序却在memB的地方Crash掉。







