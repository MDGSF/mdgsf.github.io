---
layout: post
title:  "[C/C++] array"
date:   2017-08-24
comments: true
categories: C/C++
tags: [Array]
description:
published: true
---

vector 可以动态的变大。

array 是固定大小的，就是一个普通的数组，可以使用迭代器。

## 构造函数

```c++
#include <iostream>
#include <array>

std::array<int, 3> global; //initialized as: {0,0,0}

int main()
{
    std::array<int, 3> first; //uninitialized

    std::array<int, 3> second = { 10, 20 }; //initialized as: {10,20,0}

    std::array<int, 3> third = { 1, 2, 3 }; //initialized as: {1,2,3}

    std::array<int, 3> fourth = third;

    for (auto x : fourth)
        std::cout << ' ' << x;
    std::cout << '\n';

    return 0;
}
```

## at()

返回一个引用。

```c++
#include <iostream>
#include <array>

int main()
{
    std::array<int, 10> myarray;

    for (int i = 0; i < 10; i++)
        myarray.at(i) = i + 1;

    std::cout << "myarray contains:";
    for (int i = 0; i < 10; i++)
        std::cout << ' ' << myarray.at(i);
    std::cout << '\n';

    return 0;
}
```

## front(), back()

front() 返回第一个元素的引用。

back() 返回最后一个元素的引用。

```c++
#include <iostream>
#include <array>

int main()
{
    std::array<int, 3> myarray = { 2, 16, 77 };

    std::cout << "front is: " << myarray.front() << std::endl;
    std::cout << "back is: " << myarray.back() << std::endl;

    myarray.front() = 100;
    myarray.back() = 99;

    for (int & x : myarray)
        std::cout << ' ' << x;
    std::cout << '\n';

    return 0;
}
```

## begin(), end()

```c++
#include <iostream>
#include <array>

int main()
{
    std::array<int, 5> myarray = { 2, 16, 77, 34, 50 };

    for (auto it = myarray.begin(); it != myarray.end(); ++it)
    {
        std::cout << ' ' << *it;
    }
    std::cout << '\n';

    return 0;
}
```

## size(), max_size()

size() 和 max_size() 好像是一样的。

```c++
#include <iostream>
#include <array>

int main()
{
    std::array<int, 10> myints;

    std::cout << "size of myints: " << myints.size() << std::endl; //10
    std::cout << "max_size of myints: " << myints.max_size() << std::endl; //10
    std::cout << "sizeof(myints): " << sizeof(myints) << std::endl; //40

    return 0;
}
```

## data()

返回指向第一个元素的指针。

```c++
#include <iostream>
#include <cstring>
#include <array>

int main()
{
    const char * cstr = "Test string";
    std::array<char, 12> charray;

    std::memcpy(charray.data(), cstr, 12);

    std::cout << charray.data() << '\n';

    return 0;
}
```

## operator []

```c++
#include <iostream>
#include <array>

int main()
{
    std::array<int, 10> myarray;
    unsigned int i;

    for (i = 0; i < 10; i++)
        myarray[i] = i;

    for (i = 0; i < 10; i++)
        std::cout << ' ' << myarray[i];
    std::cout << '\n';

    return 0;
}
```
