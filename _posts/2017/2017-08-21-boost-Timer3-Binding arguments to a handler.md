---
layout: post
title:  "[boost] Timer.3 - Binding arguments to a handler"
date:   2017-08-21
comments: true
categories: boost
tags: [Boost]
description:
published: true
---


项目右键点击-->属性--> C/C++ --> 常规 --> 附加包含目录

C:\local\boost_1_64_0


项目右键点击-->属性--> VC++ 目录 --> 库目录

C:\local\boost_1_64_0\lib32-msvc-14.0


```c++
#include <iostream>
#include <boost/asio.hpp>
#include <boost/bind.hpp>
#include <boost/date_time/posix_time/posix_time.hpp>

void print(const boost::system::error_code & e,
	boost::asio::deadline_timer * t,
	int * count)
{
	if (*count < 5)
	{
		std::cout << *count << std::endl;
		++(*count);
		t->expires_at(t->expires_at() + boost::posix_time::seconds(1));
		t->async_wait(boost::bind(print,
			boost::asio::placeholders::error, t, count));
	}
}

int main()
{
	boost::asio::io_service io;

	int count = 0;
	boost::asio::deadline_timer t(io, boost::posix_time::seconds(1));

	t.async_wait(boost::bind(print,
		boost::asio::placeholders::error, &t, &count));

	io.run();

	std::cout << "Final count is " << count << std::endl;

	return 0;
}
```

<a href="http://www.boost.org/doc/libs/1_64_0/doc/html/boost_asio/tutorial/tuttimer3.html" target="_blank">http://www.boost.org/doc/libs/1_64_0/doc/html/boost_asio/tutorial/tuttimer3.html</a>


