---
layout: post
title: "[Network] apr non-block"
date: 2016-05-11
author: mdgsf
comments: true
categories: network
tags: network
description:
published: true #default true
---


### Unix

| APR_SO_NONBLOCK | timeout value to apr_socket_timeout_set() | mode           |
| --------------- | ----------------------------------------- | -------------- |
|  off(==0)       |  t == 0     | non-blocking                                 |
|  off(==0)       |  t < 0      | blocking-forever                             |
|  off(==0)       |  t > 0      | blocking-with-timeout                        |
|  off(==1)       |  t == 0     | non-blocking                                 |
|  off(==1)       |  t < 0      | blocking-forever                             |
|  off(==1)       |  t > 0      | blocking-with-timeout                        |

<hr />

### Windows

| APR_SO_NONBLOCK | timeout value to apr_socket_timeout_set() | mode           |
| --------------- | ----------------------------------------- | -------------- |
|  off(==0)       |  t == 0     | blocking-forever                             |
|  off(==0)       |  t < 0      | blocking-forever                             |
|  off(==0)       |  t > 0      | blocking-with-timeout                        |
|  off(==1)       |  t == 0     | non-blocking                                 |
|  off(==1)       |  t < 0      | non-blocking                                 |
|  off(==1)       |  t > 0      | non-blocking                                 |


The default mode is APR_SO_NONBLOCK==0(off) and APR_SO_TIMEOUT==-1.

Namely, default socket is blocking-forever on both Unix and Windows.

**Conclusion** (my recommendation):

[a] When you want a **non-blocking** socket, set it to 'APR_SO_NONBLOCK==1(on) and timeout==0'.

[b] When you want a **blocking-with-timeout** socket, set it to 'APR_SO_NONBLOCK==0(off) and timeout>0'. Note that you must keep the order of calling the APIs. You must call apr_socket_opt_set(sock, APR_SO_NONBLOCK, 1) and then call apr_socket_timeout_set(sock, timeout). Otherwise, on Unix the socket becomes blocking-forever.

[c] When you want a **blocking-forever** socket, set it to 'APR_SO_NONBLOCK==0(off) and timeout<0'. In my opinion, we merely need blocking-forever sockets for real application.


### Functions

1. apr_socket_bind()
2. apr_socket_listen()
3. apr_socket_recv()
4. apr_socket_send()
5. apr_socket_accept()
6. apr_socket_connect()

We can ignore `apr_socket_bind()` and `apr_socket_listen()`. Because they never block. Simply, these APIs are always non-blocking mode.

We can control blocking/non-blocking mode for `apr_socket_recv()` and `apr_socket_send()` as I described above.

Here, we consider `apr_socket_accept()`. It is almost same as apr_socket_recv()/apr_socket_send(). The mode follows the table in the previous section. Unlike apr_socket_recv()/apr_socket_send(), blocking-forever socket is useful for apr_socket_accept(). It is because that the program might be a server process and might have nothing to do until any client connects to.

If we write a mulplexing model code, we need non-blocking socket for listening socket. We check listening socket whether it is ready to read. Readiness to read indicates that any client has connected to the socket. After we know the readiness to read, we just call apr_socket_accept(). Please look at the following example.

```cpp
apr_socket_accept(&ns, lsock, mp);
...SNIP...
/* non-blocking socket. We can't expect that @ns inherits non-blocking mode from @lsock */
apr_socket_opt_set(ns, APR_SO_NONBLOCK, 1);
apr_socket_timeout_set(ns, 0);
```

`apr_socket_connect()` is a bit different from other APIs on blocking/non-blocking mode. It has three modes, blocking-with-system-timeout, blocking-with-timeout, and non-blocking. Unlike other APIs, apr_socket_connect() never blocks forever. The default mode is blocking-with-system-timeout. The timeout value depends on OS, and it is relatively longer, e.g. over one minute. In my opinion, it is not good to use blocking-with-system-timeout mode for real applications, because it is uncontrollable. We have to set either blocking-with-timeout or non-blocking to the mode.

To make blocking-with-timeout sockets, we have to set it to 'APR_SO_NONBLOCK==1(on) and timeout>0'. As you see, this is not same as above. On Unix, we have no problem to specify 'APR_SO_NONBLOCK==0(off) and timeout>0'. Unfortunatelly, we have a problem on Windows. Setting the mode to 'APR_SO_NONBLOCK==0(off) and timeout>0' causes blocking-with-system-timeout sockets on Windows.

Conclusion: If we want blocking-with-timeout socket without portability issues, we should write code as follows:

```cpp
/* pseudo code: blocking-with-timeout apr_socket_connect() */
apr_socket_opt_set(sock, APR_SO_NONBLOCK, 1);
apr_socket_timeout_set(sock, positive_timeout);
apr_socket_connect(sock, sa);

/* still blocking-with-timeout for other operations, apr_socket_send()/apr_socket_recv() */
apr_socket_opt_set(sock, APR_SO_NONBLOCK, 0);
apr_socket_timeout_set(sock, positive_timeout);
apr_socket_send(sock, ...);
apr_socket_recv(sock, ...);
```

Blocking-with-timeout apr_socket_connect() returns APR_SUCCESS if the connection has been established successfully. Otherwise, it returns an error value. For example, APR_TIMEUP, APR_ECONNREFUSED, or APR_EHOSTUNREACH. If error value is APR_ECONNREFUSED, the server process's listen(2) backlog is beyond the limit. Please see apr_socket_listen() description above.





### 链接

[http://dev.ariel-networks.com/apr/apr-tutorial/html/apr-tutorial-13.html](http://dev.ariel-networks.com/apr/apr-tutorial/html/apr-tutorial-13.html)
