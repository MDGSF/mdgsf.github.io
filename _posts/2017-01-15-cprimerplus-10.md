---
layout: post
title:  "[C Primer Plus] 复习笔记--第10章"
date:   2017-01-15
comments: true
categories: CPrimerPlus
tags: CPrimerPlus
description:
published: true
---

### 1

常量数组

```cpp
const int days[12] = {31,28,31,30,31,30,31,31,30,31,30,31};
```

### 2

数组的初始化

> 1. 如果不初始化数组，数组的每个元素都是无用的数值。
> 2. 部分初始化数组，那么未初始化的元素被设置为零。

```cpp
int arr[10];  未初始化
int arr[10] = {100, 200};   a[0]=100, a[1]=200, 其它的都是零
int arr[10] = {0};  整个数组都是零
```

### 3

sizeof 计算数组中元素的数目。

```cpp
const int days[] = {31,28,31,30,31,30,31,31,30,31,30,31};
int nums = sizeof days / sizeof days[0];
```

sizeof 给出其后的对象或类型的大小（以字节为单位）。

### 4

float rain[5][12];

rain具有5个元素，并且每个元素都是包含12个float数值的数组。

rain[0] 是包含12个float元素的数组。

rain[0][0]  是一个float数。


### 5

**数组名==数组首元素的地址**

```
int zippo[5][2];
int (*pz)[2];
pz = zippo;


zippo == &zippo[0]
zippo[0] == &zippo[0][0]


zippo[m[n] = *(*(zippo+m)+n)
pz[m[n] = *(*(pz+m)+n)


zippo == &zippo[0]
zippo+2 == &zippo[2]
*(zippo+2) == zippo[2]
```

arr[n]的意思是 *(arr+n)，即**寻址到内存中的arr，然后移动n个单位，再取出值**。


**指针Tips：**

1. 指针的数值就是它所指的对象的地址。对于包含n个字节的数据类型，对象的地址通常指的是其首字节的地址。

2. 在指针前用运算符星号就可以得到该指针所指向的对象的值。

3. 对指针加1，等价于对指针的值加上它指向的对象的字节大小。


### 6

```cpp
int sum(int * ar, int n);

int sum(int ar[], int n);
```

在这里，int ar[] 也是表示ar是指向int的指针。


### 7

int sum(const int ar[], int n);

加上const，在该函数中则不能修改指针ar指向的内容。


### 8

```cpp
void fun(int (*pt)[4]);

void fun(int pt[][4]);
```


### 9

typedef 数组 

```cpp
typedef int arr4[4];       //arr4是4个int的数组。
typedef arr4 arr3x4[3];    //arr3x4是3个arr4数组

int sum2(arr3x4 ar, int rows);
int sum2(int ar[3][4], int rows);  //这里的3会被自动忽略，不起作用
int sum2(int ar[][4], int rows);
```

