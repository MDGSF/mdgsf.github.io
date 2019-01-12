---
layout: post
title: "Ubuntu 16.04 ip setting"
date: 2016-04-25
author: mdgsf
comments: true
categories: linux
tags: ubuntu, vbox
description: ""
published: true #default true
---


vim /etc/network/interfaces

```
# interfaces(5) file used by ifup(8) and ifdown(8)
auto lo
iface lo inet loopback
auto enp0s3
iface enp0s3 inet static
address 192.168.10.100
gateway 192.168.10.1
netmask 255.255.255.0
```

vim /etc/resolv.conf

```
nameserver 114.114.114.114
nameserver 8.8.8.8
```

ifdown enp0s3

ifup enp0s3
