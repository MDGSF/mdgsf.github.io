---
layout: post
title:  "[C] 和数组有关的一道题"
date:   2016-12-28
comments: true
categories: c
tags: c 
description: ""
published: true
---


给定一个数组a[N]，我们希望构造数组b [N]，其中b[j]=a[0]*a[1]…a[N-1] / a[j]，
在构造过程中，不允许使用除法：
要求O（1）空间复杂度和O（n）的时间复杂度；
除遍历计数器与a[N] b[N]外，不可使用新的变量（包括栈临时变量、堆空间和全局静态变量等）


```cpp
#include <stdio.h>

void Translate(int a[], int b[], int n)
{
	b[0] = 1;
	int i = 0;
	for (i = 1; i < n; i++)
	{
		b[i] = b[i-1] * a[i-1];
	}

	for (i = n-1; i >= 1; i--)
	{
		b[i] *= b[0];
		b[0] *= a[i];
	}
}

int main()
{
	int a[4] = {1, 2, 3, 4};
	int b[4] = {0};
	Translate(a, b, 4);

	for (int i = 0; i < 4; i++)
	{
		printf("%d ", b[i]);
	}
	printf("\n");

	return 0;
}
```


一共循环2次，第一次从1到n-1，第二次从n-1到1.

第一次循环，对于第k个数，是计算 0~k-1 的乘积。

第二次循环，对于第k个数，是计算 k+1~n-1 的乘积。















