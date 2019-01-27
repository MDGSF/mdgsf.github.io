---
layout: post
title:  "[boost] Timer.4 - Using a member function as a handler"
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

class printer
{
public:
	printer(boost::asio::io_service & io)
		: timer_(io, boost::posix_time::seconds(1)),
		count_(0)
	{
		timer_.async_wait(boost::bind(&printer::print, this));
	}

	~printer()
	{
		std::cout << "Final count is " << count_ << std::endl;
	}

	void print()
	{
		if (count_ < 5)
		{
			std::cout << count_ << std::endl;
			++count_;

			timer_.expires_at(timer_.expires_at() + boost::posix_time::seconds(1));
			timer_.async_wait(boost::bind(&printer::print, this));
		}
	}

private:
	boost::asio::deadline_timer timer_;
	int count_;
};

int main()
{
	boost::asio::io_service io;
	printer p(io);
	io.run();
	return 0;
}

```

<a href="http://www.boost.org/doc/libs/1_64_0/doc/html/boost_asio/tutorial/tuttimer4.html" target="_blank">http://www.boost.org/doc/libs/1_64_0/doc/html/boost_asio/tutorial/tuttimer4.html</a>


