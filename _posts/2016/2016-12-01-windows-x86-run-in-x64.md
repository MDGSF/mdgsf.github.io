---
layout: post
title:  "[Windows] 32位的程序运行在64位系统中"
date:   2016-12-01
comments: true
categories: Windows
tags: [Windows]
description:
published: true
---

通常大部分情况下，32位下的程序都能够正常的运行在64位系统中。

但是如果32位的程序需要修改调用一些系统底层的API，比如修改系统的IP地址，
禁用，启用网卡，这个时候32位的程序就无法正常使用。

### 解决方法：

将调用底层API的部分单独编一个 64 位的 api.exe，然后用32位的程序调用这个 api.exe。
