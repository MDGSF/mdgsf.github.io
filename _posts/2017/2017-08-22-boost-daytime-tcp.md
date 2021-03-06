---
layout: post
title:  "[boost] daytime tcp"
date:   2017-08-22
comments: true
categories: boost
tags: [Boost]
description:
published: true
---

## A synchronous TCP daytime client

```c++
#include <iostream>
#include <boost/array.hpp>
#include <boost/asio.hpp>

using boost::asio::ip::tcp;

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
        tcp::resolver resolver(io_service);
        tcp::resolver::query query(argv[1], "daytime");
        tcp::resolver::iterator endpoint_iterator = resolver.resolve(query);

        tcp::socket socket(io_service);
        boost::asio::connect(socket, endpoint_iterator);


        for (;;)
        {
            boost::array<char, 128> buf;
            boost::system::error_code error;

            size_t len = socket.read_some(boost::asio::buffer(buf), error);
            if (error == boost::asio::error::eof)
            {
                break;
            }
            else if (error)
            {
                throw boost::system::system_error(error);
            }

            std::cout.write(buf.data(), len);
        }

    }
    catch (std::exception e)
    {
        std::cerr << "catch error: " << e.what() << std::endl;
    }
    return 0;
}
```

## A synchronous TCP daytime server

```c++
#include <ctime>
#include <iostream>
#include <string>
#include <boost/asio.hpp>

using boost::asio::ip::tcp;

std::string make_daytime_string()
{
    using namespace std;
    time_t now = time(0);
    return ctime(&now);
}

int main()
{
    try
    {
        boost::asio::io_service io_service;
        tcp::acceptor acceptor(io_service, tcp::endpoint(tcp::v4(), 13));

        for (;;)
        {
            tcp::socket socket(io_service);
            acceptor.accept(socket);

            std::string message = make_daytime_string();

            boost::system::error_code ignored_error;
            boost::asio::write(socket, boost::asio::buffer(message), ignored_error);
        }

    }
    catch (std::exception & e)
    {
        std::cerr << "catch error: " << e.what() << std::endl;
    }
    return 0;
}

```

## An asynchronous TCP daytime server

```c++
#include <ctime>
#include <iostream>
#include <string>
#include <boost/bind.hpp>
#include <boost/shared_ptr.hpp>
#include <boost/enable_shared_from_this.hpp>
#include <boost/asio.hpp>

using boost::asio::ip::tcp;

std::string make_daytime_string()
{
    using namespace std;
    time_t now = time(0);
    return ctime(&now);
}

class tcp_connection
    : public boost::enable_shared_from_this<tcp_connection>
{
public:
    typedef boost::shared_ptr<tcp_connection> pointer;

    static pointer create(boost::asio::io_service & io_service)
    {
        return pointer(new tcp_connection(io_service));
    }

    tcp::socket & socket()
    {
        return socket_;
    }

    void start()
    {
        message_ = make_daytime_string();
        boost::asio::async_write(
            socket_,
            boost::asio::buffer(message_),
            boost::bind(
                &tcp_connection::handle_write,
                shared_from_this(),
                boost::asio::placeholders::error,
                boost::asio::placeholders::bytes_transferred
            ));
    }

private:
    tcp_connection(boost::asio::io_service & io_service)
        : socket_(io_service)
    {
    }

    void handle_write(const boost::system::error_code &, size_t)
    {
    }

    tcp::socket socket_;
    std::string message_;
};

class tcp_server
{
public:
    tcp_server(boost::asio::io_service & io_service)
        : acceptor_(io_service, tcp::endpoint(tcp::v4(), 13))
    {
        start_accept();
    }

private:
    void start_accept()
    {
        tcp_connection::pointer new_connection =
            tcp_connection::create(acceptor_.get_io_service());

        acceptor_.async_accept(
            new_connection->socket(),
            boost::bind(
                &tcp_server::handle_accept,
                this,
                new_connection,
                boost::asio::placeholders::error
            )
        );
    }

    void handle_accept(tcp_connection::pointer new_connection,
        const boost::system::error_code & error)
    {
        if (!error)
        {
            new_connection->start();
        }

        start_accept();
    }

    tcp::acceptor acceptor_;
};

int main()
{
    try
    {
        boost::asio::io_service io_service;
        tcp_server server(io_service);
        io_service.run();
    }
    catch (std::exception & e)
    {
        std::cerr << "catch error: " << e.what() << std::endl;
    }

    return 0;
}
```

<a href="http://www.boost.org/doc/libs/1_64_0/doc/html/boost_asio/tutorial.html#boost_asio.tutorial.introduction_to_sockets" target="_blank">http://www.boost.org/doc/libs/1_64_0/doc/html/boost_asio/tutorial.html#boost_asio.tutorial.introduction_to_sockets</a>
