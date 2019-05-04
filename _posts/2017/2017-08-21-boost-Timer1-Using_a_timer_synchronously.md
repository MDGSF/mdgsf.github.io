---
layout: post
title:  "[boost] Timer1 Using a timer synchronously"
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
#include <boost/date_time/posix_time/posix_time.hpp>

int main()
{
    boost::asio::io_service io;

    boost::asio::deadline_timer t(io, boost::posix_time::seconds(5));

    t.wait(); //会在这里停5秒

    std::cout << "Hello, world!" << std::endl;

    return 0;
}
```

<a href="http://www.boost.org/doc/libs/1_64_0/doc/html/boost_asio/tutorial/tuttimer1.html" target="_blank">http://www.boost.org/doc/libs/1_64_0/doc/html/boost_asio/tutorial/tuttimer1.html</a>
