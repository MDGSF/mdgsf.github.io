---
layout: post
title: "[PHP] 乱码解决方法"
date: 2016-04-21
author: mdgsf
comments: true
categories: PHP
tags: php
description:
published: true #default true
---

1.网页文件head设置编码<meta http-equiv=”Content-Type” content=”text/html; charset=utf-8″ />

2.PHP页面在保存的时候使用utf-8编码保存，可以用记事本或convertz802转换文件

3.在MYSQL中新建数据库的时候数据库 选择UTF-8编码既字符集 设定为 utf-8_unicode_ci(Unicode (多语言), 不区分大小写)，

库里面 表table的 整理 设置为 utf-8_general_ci

表里面的每个字段的 整理 都设置为 utf-8_general_ci

4.在PHP连接数据库的时候,也就是mysqli_connect()之后加入

```
//设置数据的字符集utf-8
$servername = "localhost";
$username = "root";
$password = "password";
$databasename = "form";

$conn = mysqli_connect($servername, $username, $password)
        or die('Could not connect: '. mysqli_connect_error());

mysqli_select_db($conn, $databasename);

mysqli_query($conn, "set names 'utf8'")
    or die("set names failed: " . mysqli_error($conn));

mysqli_query($conn, "set character_set_client=utf8")
    or die("set character_set_client failed: " . mysqli_error($conn));

mysqli_query($conn, "set character_set_results=utf8")
    or die("set character_set_results failed: " . mysqli_error($conn));
```

注意是utf8,不是utf-8 。

如果你的网页编码是gb2312，那就是 SET NAMES GB2312。

但编辑员强烈推荐网页编码、MySQL数据表字符集、PHPmyAdmin都统一使用UTF-8。

以上四点即可实现全站utf-8编码,而且在数据库中也不会有中文乱码。
