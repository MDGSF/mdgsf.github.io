---
layout: post
title: '[Linux] iperf 基础使用'
date: 2019-11-08
author: mdgsf
comments: true
categories: Linux
tags: [Linux]
description: ''
published: true #default true
---

iperf 是常用的带宽测试工具，ubuntu 可以直接用 apt 安装。

```shell
sudo apt-get install iperf
```

## 测试方法

现在电脑 1 上以服务端模式启动 iperf。

```shell
# iperf -s
```

然后在另一台电脑上以客户端模式启动 iperf。

```shell
# iperf -c 192.168.1.100 -t 60 -i 1
192.168.1.100 这里是电脑 1 的 IP 地址。
-t 60 测试60s
-i 1 1秒打印一次结果
```
