---
layout: post
title:  "[boost] Timer1 Using a timer asynchronously"
date:   2017-08-21
comments: true
categories: boost
tags: boost
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
#include <boost/date_time/posix_time/posix_time.hpp>

void print(const boost::system::error_code & e)
{
	std::cout << "Hello, world!" << std::endl;
}

int main()
{
	boost::asio::io_service io;

	boost::asio::deadline_timer t(io, boost::posix_time::seconds(5));

	t.async_wait(&print);

	io.run();

	return 0;
}
```

The asio library provides a guarantee that callback handlers will only be called from threads that are currently calling io_service::run(). Therefore unless the io_service::run() function is called the callback for the asynchronous wait completion will never be invoked. 


<a href="http://www.boost.org/doc/libs/1_64_0/doc/html/boost_asio/tutorial/tuttimer2.html" target="_blank">http://www.boost.org/doc/libs/1_64_0/doc/html/boost_asio/tutorial/tuttimer2.html</a>


