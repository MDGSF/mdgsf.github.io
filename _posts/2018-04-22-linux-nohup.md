---
layout: post
title: "[Linux][信号] nohup 和 &"
date: 2018-04-22
author: mdgsf
comments: true
categories: Linux
tags: linux,signal
description: ""
published: true #default true
---

## 信号

ctrl + C，发送 SIGINT 信号

关闭 ssh 终端，发送 SIGHUP 信号

### 1

./a.out &

& 会让 a.out 程序忽略 SIGINT 信号，所以执行 ctrl + C 的时候，程序不会退出。但是直接关闭 shell 的话，程序会收到 SIGHUP 信号，程序还是会退出的。

### 2

nohup ./a.out

nohup 会让 a.out 程序忽略 SIGHUP 信号，所以执行 ctrl + C 的时候，程序是会退出的。但是直接关闭 shell 的话，a.out 进程还是存在的。

### 3

nohup ./a.out &

所以同时使用 nohup 和 & 会让程序同时忽略掉信号 SIGINT 和 SIGHUP。

### 4 测试代码

```C++
#include <stdio.h>
#include <unistd.h>

int main()
{
	int x = 0;
	while (1)
	{
		printf("hello x = %d, pid = %u, ppid = %u\n", x++, getpid(), getppid());
		sleep(1);
	}
	return 0;
}
```

**Note：**另外，在使用一些开源的软件的时候，只加了 &，没有使用 nohup，发现关闭了终端之后，程序一样不会被关闭，应该就是在代码里面忽略了 SIGHUP 信号了。
