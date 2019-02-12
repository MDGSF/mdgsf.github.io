---
layout: post
title: "[Network] Reactor 模式"
date: 2019-02-12
author: mdgsf
comments: true
categories: Network
tags: [Network, Reactor]
description:
published: true #default true
---

Event loop that drives Tokio I/O resources.

This module contains Reactor, which is the event loop that drives all Tokio I/O resources. It is the reactor's job to receive events from the operating system (epoll, kqueue, IOCP, etc...) and forward them to waiting tasks. It is the bridge between operating system and the futures model.

### Overview

When using Tokio, all operations are asynchronous and represented by futures. These futures, representing the application logic, are scheduled by an executor (see runtime model for more details). Executors wait for notifications before scheduling the future for execution time, i.e., nothing happens until an event is received indicating that the task can make progress.

The reactor receives events from the operating system and notifies the executor.

Let's start with a basic example, establishing a TCP connection.

```rust
use tokio::prelude::*;
use tokio::net::TcpStream;

let addr = "93.184.216.34:9243".parse().unwrap();

let connect_future = TcpStream::connect(&addr);

let task = connect_future
    .and_then(|socket| {
        println!("successfully connected");
        Ok(())
    })
    .map_err(|e| println!("failed to connect; err={:?}", e));

tokio::run(task);
```

Establishing a TCP connection usually cannot be completed immediately. TcpStream::connect does not block the current thread. Instead, it returns a future that resolves once the TCP connection has been established. The connect future itself has no way of knowing when the TCP connection has been established.

Before returning the future, TcpStream::connect registers the socket with a reactor. This registration process, handled by Registration, is what links the TcpStream with the Reactor instance. At this point, the reactor starts listening for connection events from the operating system for that socket.

Once the connect future is passed to tokio::run, it is spawned onto a thread pool. The thread pool waits until it is notified that the connection has completed.

When the TCP connection is established, the reactor receives an event from the operating system. It then notifies the thread pool, telling it that the connect future can complete. At this point, the thread pool will schedule the task to run on one of its worker threads. This results in the and_then closure to get executed.

### 链接

[https://docs.rs/tokio/0.1/tokio/reactor/index.html](https://docs.rs/tokio/0.1/tokio/reactor/index.html)