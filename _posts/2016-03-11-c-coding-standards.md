---
layout: post
title:  "[C++] Coding Standards"
date:   2016-03-11
comments: true
categories: c
tags: c, coding standard
description:
published: true
---


### 1. #defines and  const

test.h

```cpp
#ifndef TEST_H
#define TEST_H
#endif
```

```cpp
#define FALSE 0
#define TRUE (!FALSE)
```

```cpp
#define MAX_NO_OF_STUDENT 100
const int MAX_NO_OF_STUDENT = 100;
```

### 2.Function（函数）

```cpp
int m_iGetValues();
m_ : a member of a class
i  : returns a int
```

```cpp
long m_lGetValues();
m_ : a member of a class
l  : returns a long 
```

```cpp
int * m_piGetValus();
m_ : a member of a class
p  : returns pointer
i  : a int pointer
```

```cpp
void * m_pvDoSomething();
m_ : a member of a class
p  : returns pointer
v  : a void pointer
```

```cpp
unsigned char ucGetValues();
uc : This returns unsigned char
```

```cpp
bool bIsRunning();
b : returns a bool 
```

```cpp
void m_vSetValues(int iNum);
m_ : a member of a class
v  : there is no return value
```

If the function is not a member of a class, then 'm_' prefix is not used.Example:

```cpp
//global function
int g_iGetValues();
g_ : a global functions 
i  : returns a int
```

### 3.Variables（变量）

```cpp
//在 int变量 的最前面加上一个小写的i,之后每个单词的第一个字母大写
int iNum;
int iNoOfVariables;
```

```cpp
//在数组的最前面加上a(array),然后紧接着一个数组类型的字母。
char acFileName[128];  ac代表一个字符类型的数组
int aiNum[128];  ai代表这是一个int类型的数组
char *apcFileName[128];  这是一个数组，数组中保存着128个char类型的指针
char (*pacFileName)[128];  这是一个指针，
```

```cpp
//在变量的最前面加上 m_， 代表这是一个类或者是一个结构体中的成员变量。
typedef struct _SNode {
    char * m_pcName;  //m_:结构体成员; pc:指向char类型的指针
    struct _SNode * m_pstNext;  //m_结构体成员; pst:指向struct类型的指针
}SNode;
```
