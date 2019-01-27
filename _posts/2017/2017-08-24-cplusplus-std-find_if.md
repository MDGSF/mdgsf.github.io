---
layout: post
title:  "[C/C++] find_if"
date:   2017-08-24
comments: true
categories: C/C++
tags: [Find_if]
description:
published: true
---

返回第一个为true的元素。

找不到则返回最后一个元素。

```c++
#include <iostream>
#include <algorithm>
#include <vector>

bool IsOdd(int i)
{
	return ((i % 2) == 1);
}

int main()
{
	std::vector<int> myvector;
	myvector.push_back(10);
	myvector.push_back(25);
	myvector.push_back(40);
	myvector.push_back(55);

	std::vector<int>::iterator it = std::find_if(myvector.begin(), myvector.end(), IsOdd);
	std::cout << "The first odd value is " << *it << '\n'; //25

	return 0;
}

```
