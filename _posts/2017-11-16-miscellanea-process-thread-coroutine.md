---
layout: post
title:  "进程、线程和协程"
date:   2017-11-16
comments: true
categories: miscellanea
tags: process,thread,coroutine
description:
published: true
---

## 进程、线程和协程

**进程**拥有自己独立的堆和栈，既不共享堆，亦不共享栈，进程由操作系统调度。

**线程**拥有自己独立的栈和共享的堆，共享堆，不共享栈，线程亦由操作系统调度(标准线程是的)。

**协程**和线程一样共享堆，不共享栈，协程由程序员在协程的代码里显示调度。


协程和线程的区别是：协程避免了无意义的调度，由此可以提高性能，但也因此，程序员必须自己承担调度的责任，同时，协程也失去了标准线程使用多CPU的能力。

进程和线程都是由操作系统来调用的

协程是协作式调度的

go语言的协程Goroutines是语言层面支持的，不需要开发人员去调度，使用起来就像是线程一样。



## Goroutines

https://golang.org/doc/effective_go.html#goroutines

They're called goroutines because the existing terms—threads, coroutines, processes, and so on—convey inaccurate connotations. A goroutine has a simple model: it is a function executing concurrently with other goroutines in the same address space. It is lightweight, costing little more than the allocation of stack space. And the stacks start small, so they are cheap, and grow by allocating (and freeing) heap storage as required.

Goroutines are multiplexed onto multiple OS threads so if one should block, such as while waiting for I/O, others continue to run. Their design hides many of the complexities of thread creation and management. 


## 并发与并行

<img src="{{ site.url }}/images/2017/11/1601.png" alt="1601" />

<img src="{{ site.url }}/images/2017/11/1602.jpg" alt="1602" />


你吃饭吃到一半，电话来了，你一直到吃完了以后才去接，这就说明你不支持并发也不支持并行。

你吃饭吃到一半，电话来了，你停了下来接了电话，接完后继续吃饭，这说明你支持并发。

你吃饭吃到一半，电话来了，你一边打电话一边吃饭，这说明你支持并行。

并发的关键是你有处理多个任务的能力，不一定要同时。

并行的关键是你有同时处理多个任务的能力。

所以我认为它们最关键的点就是：是否是『同时』。


https://talks.golang.org/2012/waza.slide



