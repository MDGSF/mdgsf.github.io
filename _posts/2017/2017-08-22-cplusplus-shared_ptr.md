---
layout: post
title:  "[C/C++] shared_ptr"
date:   2017-08-22
comments: true
categories: C/C++
tags: [Shared_ptr]
description:
published: true
---

## 构造函数

```c++
#include <iostream>
#include <memory>

struct C { int * data; };

int main()
{
    std::shared_ptr<int> p1;
    std::shared_ptr<int> p2(nullptr);
    std::shared_ptr<int> p3(new int);

    std::cout << "p1: " << p1.use_count() << "\n"; // 0
    std::cout << "p2: " << p2.use_count() << "\n"; // 0
    std::cout << "p3: " << p3.use_count() << "\n"; // 1


    std::shared_ptr<int> p4(new int, std::default_delete<int>());
    std::shared_ptr<int> p5(new int, [](int*p) {delete p; }, std::allocator<int>());
    std::shared_ptr<int> p6(p5);

    std::cout << "p4: " << p4.use_count() << "\n"; // 1
    std::cout << "p5: " << p5.use_count() << "\n"; // 2
    std::cout << "p6: " << p6.use_count() << "\n"; // 2


    std::shared_ptr<int> p7(std::move(p6));

    std::cout << "p7: " << p7.use_count() << "\n"; // 2
    std::cout << "p6: " << p6.use_count() << "\n"; // 0


    std::shared_ptr<int> p8(std::unique_ptr<int>(new int));
    std::shared_ptr<C> obj(new C);
    std::shared_ptr<int> p9(obj, obj->data);
    std::cout << "p8: " << p8.use_count() << "\n"; // 1
    std::cout << "p9: " << p9.use_count() << "\n"; // 2

    return 0;
}
```

use_count() 应该就是返回指向该内存的指针的数量。

## get()

获取指针的值

```c++
#include <iostream>
#include <memory>

int main()
{
    int * p = new int(10);
    std::shared_ptr<int> a(p);

    if (a.get() == p)
    {
        std::cout << "a and p point to the same location\n";
    }

    std::cout << *a.get() << "\n";
    std::cout << *a << "\n";
    std::cout << *p << "\n";

    return 0;
}
/*
output:
a and p point to the same location
10
10
10
*/
```

## unique()

如果 use_count() == 1 返回true。否则返回false。

```c++
#include <iostream>
#include <memory>

int main()
{
    std::shared_ptr<int> foo;
    std::shared_ptr<int> bar(new int);

    std::cout << "1 foo: " << foo.unique() << "\n"; // 0
    std::cout << "1 bar: " << bar.unique() << "\n"; // 1

    foo = bar;
    std::cout << "2 foo: " << foo.unique() << "\n"; // 0
    std::cout << "2 bar: " << bar.unique() << "\n"; // 0

    bar = nullptr;
    std::cout << "2 foo: " << foo.unique() << "\n"; // 1
    std::cout << "2 bar: " << bar.unique() << "\n"; // 0

    return 0;
}
```

## operator bool

重载bool运算符

```c++
#include <iostream>
#include <memory>

int main()
{
    std::shared_ptr<int> foo;
    std::shared_ptr<int> bar(new int(34));

    if (foo) std::cout << "foo points to " << *foo << "\n";
    else std::cout << "foo is null\n";

    if (bar) std::cout << "bar points to " << *bar << '\n';
    else std::cout << "bar is null\n";

    return 0;
}
```

## operator *

重载取值运算符

```c++
#include <iostream>
#include <memory>

int main()
{
    std::shared_ptr<int> foo(new int);
    std::shared_ptr<int> bar(new int(100));

    *foo = *bar * 2;

    std::cout << "foo: " << *foo << "\n"; //200
    std::cout << "bar: " << *bar << "\n"; //100

    return 0;
}
```

## operator ->

It returns the same value as get().

```c++
#include <iostream>
#include <memory>

struct C { int a; int b; };

int main()
{
    std::shared_ptr<C> foo;
    std::shared_ptr<C> bar(new C);

    foo = bar;

    foo->a = 10;
    foo->b = 20;

    std::cout << "foo: " << foo->a << " " << foo->b << "\n";
    std::cout << "bar: " << bar->a << " " << bar->b << "\n";

    return 0;
}
```

## operator =

```c++
#include <iostream>
#include <memory>

int main()
{
    std::shared_ptr<int> foo;
    std::shared_ptr<int> bar(new int(10));

    foo = bar; //copy

    bar = std::make_shared<int>(20); //move

    std::unique_ptr<int> unique(new int(30));
    foo = std::move(unique); //move from unique_ptr

    std::cout << "*foo: " << *foo << '\n'; //30
    std::cout << "*bar: " << *bar << '\n'; //20

    return 0;
}
```

## reset()

```c++
#include <iostream>
#include <memory>

int main()
{
    std::shared_ptr<int> sp;

    sp.reset(new int);
    *sp = 10;
    std::cout << *sp << '\n'; //10

    sp.reset(new int);
    *sp = 20;
    std::cout << *sp << '\n'; //20

    sp.reset();

    return 0;
}
```

## swap()

```c++
#include <iostream>
#include <memory>

int main()
{
    std::shared_ptr<int> foo(new int(10));
    std::shared_ptr<int> bar(new int(20));

    foo.swap(bar);

    std::cout << "*foo: " << *foo << '\n'; //20
    std::cout << "*bar: " << *bar << '\n'; //10

    return 0;
}
```

## 注意点1

```c++
#include <iostream>
#include <memory>

int main()
{
    int * pInt = new int[100];
    std::shared_ptr<int> sp1(pInt);
    std::shared_ptr<int> sp2(pInt); //会Crash，因为内存被释放了两次。
    return 0;
}
```

## 注意点2

```c++
#include <iostream>
#include <memory>

class tester
{
public:
    tester(){}
    ~tester() {}

    std::shared_ptr<tester> getTester()
    {
        return std::shared_ptr<tester>(this);
    }
};

int main()
{
    tester t;
    std::shared_ptr<tester> sp = t.getTester(); //会crash，析构函数释放了一次，shared_ptr释放了一次。
    return 0;
}
```

要用enable_shared_from_this
