---
layout: post
title: "[PHP] not allowed to connect to this MySQL"
date: 2016-04-26
author: mdgsf
comments: true
categories: PHP
tags: [Php,Mysql]
description:
published: true #default true
---

电脑A： 192.168.10.105

电脑B： 192.168.10.100

mysql 安装在 电脑A 上，在电脑A上可以用localhost访问mysqli

但是在 电脑B 上，通过连接到电脑A的ip却无法访问。

解决方法：

在电脑A上，用localhost进入数据库

需更改 mysql 数据库里的 user表里的 host 项

把 localhost 改称 %

然后重启 mysql 就可以了。
