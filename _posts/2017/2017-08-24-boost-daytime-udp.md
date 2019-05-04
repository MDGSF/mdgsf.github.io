---
layout: post
title:  "[boost] daytime udp"
date:   2017-08-24
comments: true
categories: boost
tags: [Boost]
description:
published: true
---

## A synchronous UDP daytime client

```c++
#include <iostream>
#include <boost/array.hpp>
#include <boost/asio.hpp>

using boost::asio::ip::udp;

int main(int argc, char * argv[])
{
    try
    {
        if (argc != 2)
        {
            std::cerr << "Usage: client <host>" << std::endl;
            return 1;
        }

        boost::asio::io_service io_service;
        udp::resolver resolver(io_service);
        udp::resolver::query query(udp::v4(), argv[1], "daytime");

        udp::endpoint receiver_endpoint = *resolver.resolve(query);

        udp::socket socket(io_service);
        socket.open(udp::v4());

        boost::array<char, 1> send_buf = { {0} };
        socket.send_to(boost::asio::buffer(send_buf), receiver_endpoint);

        boost::array<char, 128> recv_buf;
        udp::endpoint sender_endpoint;
        size_t len = socket.receive_from(boost::asio::buffer(recv_buf), sender_endpoint);

        std::cout.write(recv_buf.data(), len);
    }
    catch (std::exception & e)
    {
        std::cerr << "catch error: " << e.what() << std::endl;
    }
    return 0;
}
```

## A synchronous UDP daytime server

```c++
#include <ctime>
#include <iostream>
#include <string>
#include <boost/array.hpp>
#include <boost/asio.hpp>

using boost::asio::ip::udp;

std::string make_daytime_string()
{
    using namespace std;
    time_t now = time(0);
    return ctime(&now);
}

int main(int argc, char * argv[])
{
    try
    {
        boost::asio::io_service io_service;
        udp::socket socket(io_service, udp::endpoint(udp::v4(), 13));
        for (;;)
        {
            boost::array<char, 1> recv_buf;
            udp::endpoint remote_endpoint;
            boost::system::error_code error;

            socket.receive_from(boost::asio::buffer(recv_buf),
                remote_endpoint, 0, error);
            if (error && error != boost::asio::error::message_size)
                throw boost::system::system_error(error);

            std::string message = make_daytime_string();

            boost::system::error_code ignored_error;
            socket.send_to(boost::asio::buffer(message),
                remote_endpoint, 0, ignored_error);
        }
    }
    catch (std::exception & e)
    {
        std::cerr << e.what() << std::endl;
    }
    return 0;
}
```

## An asynchronous UDP daytime server

```c++
#include <ctime>
#include <iostream>
#include <string>
#include <boost/array.hpp>
#include <boost/bind.hpp>
#include <boost/shared_ptr.hpp>
#include <boost/asio.hpp>

using boost::asio::ip::udp;

std::string make_daytime_string()
{
    using namespace std;
    time_t now = time(0);
    return ctime(&now);
}

class udp_server
{
public:
    udp_server(boost::asio::io_service & io_service)
        : socket_(io_service, udp::endpoint(udp::v4(), 13))
    {
        start_receive();
    }

private:
    void start_receive()
    {
        socket_.async_receive_from(
            boost::asio::buffer(recv_buffer_),
            remote_endpoint,
            boost::bind(&udp_server::handle_receive,
                this,
                boost::asio::placeholders::error,
                boost::asio::placeholders::bytes_transferred)
        );
    }

    void handle_receive(const boost::system::error_code & error, std::size_t)
    {
        if (!error || error == boost::asio::error::message_size)
        {
            boost::shared_ptr<std::string> message(
                new std::string(make_daytime_string())
            );

            socket_.async_send_to(
                boost::asio::buffer(*message),
                remote_endpoint,
                boost::bind(&udp_server::handle_send,
                    this,
                    message,
                    boost::asio::placeholders::error,
                    boost::asio::placeholders::bytes_transferred)
            );

            start_receive();
        }
    }

    void handle_send(boost::shared_ptr<std::string>,
        const boost::system::error_code &,
        std::size_t)
    {

    }

    udp::socket socket_;
    udp::endpoint remote_endpoint;
    boost::array<char, 1> recv_buffer_;
};

int main()
{
    try
    {
        boost::asio::io_service io_service;
        udp_server server(io_service);
        io_service.run();
    }
    catch (std::exception & e)
    {
        std::cerr << e.what() << std::endl;
    }
    return 0;
}
```

<a href="http://www.boost.org/doc/libs/1_64_0/doc/html/boost_asio/tutorial.html#boost_asio.tutorial.introduction_to_sockets" target="_blank">http://www.boost.org/doc/libs/1_64_0/doc/html/boost_asio/tutorial.html#boost_asio.tutorial.introduction_to_sockets</a>
