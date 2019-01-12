---
layout: post
title: "[PHP] not allowed to connect to this MySQL"
date: 2016-04-26
author: mdgsf
comments: true
categories: php
tags: php,mysql
description:
published: true #default true
---

电脑A： 192.168.10.105
电脑B： 192.168.10.100

mysql安装在 电脑A 上，在电脑A上可以用localhost访问mysqli

但是在 电脑B 上，通过连接到电脑A的ip却无法访问。

解决方法：

在电脑A上，用localhost进入数据库

需更改 mysql 数据库里的 user表里的 host项

把localhost改称%

然后重启mysql就可以了。
