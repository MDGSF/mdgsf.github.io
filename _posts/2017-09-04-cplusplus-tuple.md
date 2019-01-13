---
layout: post
title:  "[C/C++] tuple"
date:   2017-09-04
comments: true
categories: C/C++
tags: tuple
description:
published: true
---


## 1

```c++
#include <iostream>
#include <tuple>
using namespace std;

int main()
{
	std::tuple<int, char> foo(10, 'x');

	auto bar = std::make_tuple("text", 3.1, 14, 'y');

	std::get<2>(bar) = 100;

	int myint;
	char mychar;

	std::tie(myint, mychar) = foo;
	cout << "myint = " << myint << ", mychar = " << mychar << endl;

	std::tie(std::ignore, std::ignore, myint, mychar) = bar;
	cout << "myint = " << myint << ", mychar = " << mychar << endl;

	mychar = std::get<3>(bar);

	std::get<0>(foo) = std::get<2>(bar);

	std::get<1>(foo) = mychar;

	std::cout << "foo contains: ";
	std::cout << std::get<0>(foo) << ' ';
	std::cout << std::get<1>(foo) << '\n';

	return 0;
}
```

## 2 tuple_element

```c++
#include <iostream>
#include <tuple>
using namespace std;

int main()
{
	auto mytuple = std::make_tuple(10, 'a');

	std::tuple_element<0, decltype(mytuple)>::type first = std::get<0>(mytuple);
	std::tuple_element<1, decltype(mytuple)>::type second = std::get<1>(mytuple);

	cout << "mytuple contains: " << first << " and " << second << '\n';

	return 0;
}
```

## 3 tuple_size()

```c++
#include <iostream>
#include <tuple>
using namespace std;

int main()
{
	std::tuple<int, char, double> mytuple(10, 'a', 3.14);

	cout << "mytuple has ";
	cout << std::tuple_size<decltype(mytuple)>::value;
	cout << " elements.\n";

	return 0;
}
```


