---
layout: post
title:  "[boost] foreach"
date:   2017-08-21
comments: true
categories: boost
tags: boost
description:
published: true
---


项目右键点击-->属性--> C/C++ --> 常规 --> 附加包含目录

C:\local\boost_1_64_0

```c++
#include <boost/lambda/lambda.hpp>
#include <iostream>
#include <iterator>
#include <algorithm>

int main()
{
	using namespace boost::lambda;
	typedef std::istream_iterator<int> in;

	std::for_each(
		in(std::cin), in(), std::cout << (_1 * 3) << " "
	);
}
```



