---
layout: post
title:  "[C] Pointer and Array 1"
date:   2016-09-29
comments: true
categories: c
tags: c
description:
published: true
---



```cpp
#include <stdio.h>

#define SIZE 3

int main()
{
	printf("demo\n");
	int valida[SIZE] = {0, 1, 2, };

	int a[SIZE] = {0, 1, 2};
	int * pi = a;
	int (*pa)[SIZE] = &a;

	int i = 0; 
	for (i = 0; i < SIZE; i++)
	{
		printf("a[%d] = %d\t", i, a[i]);
	}
	printf("\n");
	for (i = 0; i <= SIZE; i++)
	{
		printf("%p\t", &a[i]);
	}
	printf("\n");
	for (i = 0; i < SIZE; i++)
	{
		printf("*(a+%d) = %d\t", i, *(a+i) );
	}
	printf("\n");
	for (i = 0; i < SIZE; i++)
	{
		printf("*(pi+%d) = %d\t", i, *(pi+i) );
	}
	printf("\n");

	printf("sizeof(a) = %d\n", sizeof(a));
	printf("sizeof(pi) = %d\n\n", sizeof(pi));

	printf("a = %p\n", a);
	printf("pi = %p\n", pi);
	printf("&a = %p\n", &a);
	printf("pa = %p\n", pa);

	printf("a+1 = %p\n", a+1);
	printf("pi+1 = %p\n", pi+1);
	printf("&a + 1 = %p\n", &a + 1);
	printf("pa + 1 = %p\n", pa + 1);

	return 0;
}
```
