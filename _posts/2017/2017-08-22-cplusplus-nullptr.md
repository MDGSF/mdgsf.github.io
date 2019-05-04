---
layout: post
title:  "[C/C++] NULL,nullptr,nullptr_t"
date:   2017-08-22
comments: true
categories: C/C++
tags: [Nullptr]
description:
published: true
---

## NULL

**Possible implementation in C++**

```c++
#define NULL 0
//since C++11
#define NULL nullptr
```

**Notes**

In C, the macro NULL may have the type void*, but that is not allowed in C++.

```c++
#ifndef NULL
    #ifdef __cplusplus
        #define NULL 0 //c++中定义为0
    #else
        #define NULL ((void *)0) //c语言中定义为void*
    #endif
#endif
```

**NULL 问题**

```c++
#include <cstddef>
#include <iostream>

void test(int i)
{
    std::cout << "int i\n";
}

void test(int * i)
{
    std::cout << "int * i\n";
}

int main()
{
    test(0); //int i
    test(NULL); //int i，在这里会被当做0来使用。
    return 0;
}
```

## nullptr 是nullptr_t类型的常量

```c++
#include <cstddef>
#include <iostream>

template<class F, class A>
void Fwd(F f, A a)
{
    f(a);
}

void g(int * i)
{
    std::cout << "Function g called\n";
}

int main()
{
    g(NULL); //Function g called
    g(0); //Function g called
    Fwd(g, nullptr); //Function g called
    //Fwd(g, NULL); //“void (int *)”: 无法将参数 1 从“int”转换为“int *”
}
```

## nullptr_t 是一个类型

```c++
#include <cstddef>
#include <iostream>

using namespace std;

void f(int* pi)
{
    std::cout << "Pointer to integer overload\n";
}

void f(double* pd)
{
    std::cout << "Pointer to double overload\n";
}

void f(std::nullptr_t nullp)
{
    std::cout << "null pointer overload\n";
}

int main()
{
    int* pi = NULL;
    double* pd = NULL;

    f(pi);
    f(pd);
    f(nullptr);  // would be ambiguous without void f(nullptr_t)
                 // f(0);  // ambiguous call: all three functions are candidates
                 // f(NULL); // ambiguous if NULL is an integral null pointer constant
                 // (as is the case in most implementations)
}
```

## nullptr_t 实现

```c++
class nullptr_t
{
public:
    template<class T>
    inline operator T*() const    //定义类型转换操作符，使nullptr_t 可转为任意非类成员指针类型
    { return 0; }


    //重载类型转换操作符，使 nullptr_t 可以转换为类 C 中任意的指针类型（数据成员指针/函数成员指针）
    //对类中数据成员的指针，T 将被推导为数据成员的类型 eg: int (X::*); 此时 T 为 int,C 为 X
    //对类中函数成员的指针，T 将被推导为函数成员的类型 eg: int (X::*)(int); 此时T 等效于： typedef int (T)(int)
    template<class C, class T>
    inline operator T C::*() const
    { return 0; }
private:
    void operator&() const;
};

const null_ptr nullptr = {}
```
