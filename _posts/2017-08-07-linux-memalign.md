---
layout: post
title:  "[Linux] memalign 和 posix_memalign"
date:   2017-08-07
comments: true
categories: Linux
tags: memalign, posix_memalign
description:
published: true
---


## memalign

在GNU系统中，malloc或realloc返回的内存块地址都是8的倍数（如果是64位系统，则为16的倍数）。如果你需要更大的粒度，请使用memalign或valloc。这些函数在头文件“stdlib.h”中声明。

在GNU库中，可以使用函数free释放memalign和valloc返回的内存块。但无法在BSD系统中使用，而且BSD系统中并未提供释放这样的内存块的途径。

函数：void * memalign (size_t boundary, size_t size) 
函数memalign将分配一个由size指定大小，地址是boundary的倍数的内存块。参数boundary必须是2的幂！函数memalign可以分配较大的内存块，并且可以为返回的地址指定粒度。

函数：void * valloc (size_t size) 
使用函数valloc与使用函数memalign类似，函数valloc的内部实现里，使用页的大小作为对齐长度，使用memalign来分配内存。它的实现如下所示： 

```c
void * valloc (size_t size)
{
    return memalign (getpagesize (), size);
}
```


## posix_memalign

### SYNOPSIS

```c
#include <stdlib.h>
int posix_memalign(void **memptr, size_t alignment, size_t size); [Option End]
```
 

### DESCRIPTION

The posix_memalign() function shall allocate size bytes aligned on a boundary specified by alignment, and shall return a pointer to the allocated memory in memptr. The value of alignment shall be a multiple of sizeof( void *), that is also a power of two. Upon successful completion, the value pointed to by memptr shall be a multiple of alignment.

The free() function shall deallocate memory that has previously been allocated by posix_memalign().

### RETURN VALUE

Upon successful completion, posix_memalign() shall return zero; otherwise, an error number shall be returned to indicate the error.


