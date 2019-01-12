---
layout: post
title:  "[C] sizeof struct"
date:   2017-02-24
comments: true
categories: c
tags: c 
description: ""
published: true
---


### 内存对齐规则

**1**

第一个数据成员放在offset为0的地方，以后每个数据成员的
起始位置要从该成员大小的整数倍开始，比如int在32位机为4字节，
则要从4的整数倍开始存储。

**2**

如果是结构体作为成员，则该结构体成员要从其内部最大元素大小
的整数倍地址开始存储。

例如：struct a里有struct b，struct b里有char，int，double，
那么b应该从8的整数倍开始存储。

**3**

结构体的总大小，也就是sizeof的结果，必须是其内部最大成员
的整数倍，不足的要补齐。

#pragma pack(1) 
告诉编译器都按照1的整数倍对齐，换句话说就是没有对齐规则。

PS：VC和VS等IDE默认是#pragma pack(8)，所以测试我们的规则会
正常。gcc默认是#pragma pack(4)，并且gcc只支持1,2,4对齐。
套用以上之原则计算对齐值是不能大于#pragma pack指定的值。



```cpp
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MyOffset(st, mem) \
	(int)(&(((st*)0)->mem)) 

/*
* #pragma pack(1) : 16 32
* #pragma pack(2) : 16 32
* #pragma pack(4) : 16 36
* #pragma pack(8) : 24 48
*/

#pragma pack(8)

typedef struct bb
{
	int id;
	double weight;
	float height;
}BB;

typedef struct aa
{
	char name[2];
	int id;
	double score;
	short grade;
	BB b;
}AA;

int main()
{
	BB b;
	AA a;

	printf("sizeof(b) = %d\n", sizeof(b));
	printf("sizeof(a) = %d\n", sizeof(a));

	printf("MyOffset(BB, id): %d\n", MyOffset(BB, id));
	printf("MyOffset(BB, weight): %d\n", MyOffset(BB, weight));
	printf("MyOffset(BB, height): %d\n", MyOffset(BB, height));

	printf("MyOffset(AA, name): %d\n", MyOffset(AA, name));
	printf("MyOffset(AA, id): %d\n", MyOffset(AA, id));
	printf("MyOffset(AA, score): %d\n", MyOffset(AA, score));
	printf("MyOffset(AA, grade): %d\n", MyOffset(AA, grade));
	printf("MyOffset(AA, b): %d\n", MyOffset(AA, b));
	return 0;
}

```


<a href="http://blog.csdn.net/hairetz/article/details/4084088" target="_blank">http://blog.csdn.net/hairetz/article/details/4084088</a>


