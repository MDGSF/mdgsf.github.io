---
layout: post
title:  "[C/C++] bind function lambda"
date:   2017-08-24
comments: true
categories: C/C++
tags: bind
description:
published: true
---


## bind

```c++
#include <iostream>
#include <functional>

double my_divide(double x, double y)
{
	return x / y;
}

struct MyPair
{
	double a;
	double b;
	double multiply()
	{
		return a * b;
	}
};

int main()
{
	using namespace std::placeholders;

	auto fn_five = std::bind(my_divide, 10, 2); //return 10/2
	std::cout << fn_five() << "\n";

	auto fn_half = std::bind(my_divide, _1, 2); //return x/2
	std::cout << fn_half(10) << "\n";

	auto fn_invert = std::bind(my_divide, _2, _1); //return y/x
	std::cout << fn_invert(10, 2) << '\n';

	auto fn_rounding = std::bind<int>(my_divide, _1, _2); //return int(10/3)
	std::cout << fn_rounding(10, 3) << "\n";

	MyPair ten_two{ 10, 2 };
	auto bound_member_fn = std::bind(&MyPair::multiply, _1); //return x.multiply()
	std::cout << bound_member_fn(ten_two) << '\n';

	auto bound_member_data = std::bind(&MyPair::a, ten_two); //return ten_two.a
	std::cout << bound_member_data() << '\n';

	auto bound_member_data_2 = std::bind(&MyPair::a, _1); //return x.a
	std::cout << bound_member_data(ten_two) << '\n';

	return 0;
}
```


## function

```c++
#include <iostream>
#include <functional>

std::function<size_t(const char*)> print_func;

size_t CPrint(const char * pcStr)
{
	std::cout << pcStr << '\n';
	return 0;
}

class CxxPrint
{
public:
	size_t operator()(const char * pcStr)
	{
		std::cout << pcStr << '\n';
		return 0;
	}
};

int main()
{
	print_func = CPrint;
	print_func("Hello World");

	CxxPrint p;
	print_func = p;
	print_func("Hello CxxPrint");
	return 0;
}
```


## lambda

```c++
#include <iostream>
#include <algorithm>
#include <vector>
#include <functional>

// 4. capture of class member
class A
{
public:
	//capture member by this
	void DoSomethins1()
	{
		auto it = std::find_if(m_vec.begin(), m_vec.end(), [this](int i) {
			return i > m_min_val && i < m_max_val;
		});
	}

	//capture member by default pass-by-value
	void DoSomethins2()
	{
		auto it = std::find_if(m_vec.begin(), m_vec.end(), [=](int i) {
			return i > m_min_val && i < m_max_val;
		});
	}

	//capture member by default pass-by-reference
	void DoSomethins3()
	{
		auto it = std::find_if(m_vec.begin(), m_vec.end(), [&](int i) {
			return i > m_min_val && i < m_max_val;
		});
	}

private:
	std::vector<int> m_vec;
	int m_min_val;
	int m_max_val;
};

int main()
{
	//1. simple lambda
	std::vector<int> myvector;
	myvector.push_back(10);
	myvector.push_back(25);
	myvector.push_back(40);
	myvector.push_back(55);

	std::vector<int>::iterator it = std::find_if(myvector.begin(), myvector.end(), [](int i) {
		return ((i % 2) == 1);
	});
	std::cout << "The first odd value is " << *it << '\n'; //25

	//2. lambda return syntax
	std::function<int(int)> square = [](int i) -> int { return i * i; };
	std::cout << square(10) << "\n";


	//3. capture of local variable
	/*
	[a,&b] 其中 a 以复制捕获而 b 以引用捕获。
	[this] 以引用捕获当前对象（ *this ）
	[&] 以引用捕获所有用于 lambda 体内的自动变量，并以引用捕获当前对象，若存在
	[=] 以复制捕获所有用于 lambda 体内的自动变量，并以引用捕获当前对象，若存在
	[] 不捕获
	*/
	{
		int min_val = 10;
		int max_val = 100;

		auto it = std::find_if(myvector.begin(), myvector.end(), [=](int i) {
			return i > min_val && i < max_val;
		});

		auto it1 = std::find_if(myvector.begin(), myvector.end(), [&](int i) {
			return i > min_val && i < max_val;
		});

		auto it2 = std::find_if(myvector.begin(), myvector.end(), [=, &max_val](int i) {
			return i > min_val && i < max_val;
		});
	}

	return 0;
}
```

