---
layout: post
title: "[PHP] 开启debug模式"
date: 2016-04-26
author: mdgsf
comments: true
categories: php
tags: php
description:
published: true #default true
---


vim /etc/php/7.0/fpm/php.ini

找到 display_errors ，修改为

display_errors = on ， 保存

重启 php

/etc/init.d/php7.0-fpm restart

重启 nginx

nginx -s reload


### 错误代码

```php
<?php

$firstName = 'Andy';
$nameLength = str_len($firstName);
$myVar = 'Hi, my name is ' . $nameLength . ' letters long.';
echo $myVar;
```


错误如下：

```
Fatal error: Uncaught Error: Call to undefined function str_len() in /mnt/share/PHP/www/errorDisplay/index.php:4 Stack trace: #0 {main} thrown in /mnt/share/PHP/www/errorDisplay/index.php on line 4
```

php 的配置文件: **/etc/php/7.0/fpm/php.ini**

php 的log文件: **/var/log/php7.0-fpm.log**



nginx 的配置文件: **/etc/nginx/nginx.conf**

nginx 的log文件:

**/etc/log/nginx/access.log**

**/etc/log/nginx/error.log**
