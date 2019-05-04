---
layout: post
title:  "[C/C++] 指针的引用作为参数"
date:   2016-06-24
comments: true
categories: C/C++
tags: [C++]
description:
published: true
---

```cpp
#include <stdio.h>

int a = 1;
int b = 2;
int c = 3;

void vTest1(int * pi)
{
    pi = &b;
    printf("vTest1() *pi = %d\n", *pi);
}

void vTest2(int * & rpi)
{
    rpi = &c;
    printf("vTest2() *rpi = %d\n", *rpi);
}

int main()
{
    int * pi = &a;
    printf("*pi = %d\n", *pi);

    vTest1(pi);
    printf("main() vTest1 *pi = %d\n", *pi);

    vTest2(pi);
    printf("main() vTest2 *pi = %d\n", *pi);

    return 0;
}
```

输出：

```
*pi = 1
vTest1() *pi = 2
main() vTest1 *pi = 1
vTest2() *rpi = 3
main() vTest2 *pi = 3
```

在 vTest1() 中, 当我们把一个指针做为参数传一个方法时，其实是把指针的复本传递给了 vTest1()，

也就是说 **是对指针进行了值传递**。

在方法里做修改只是修改的指针的copy而不是指针本身，原来的指针还保留着原来的值。

vText2() 则是对指针的引用。
