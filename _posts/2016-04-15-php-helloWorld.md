---
layout: post
title: "[PHP] 入门 Hello World"
date: 2016-04-15
author: mdgsf
comments: true
categories: PHP
tags: php
description:
published: true #default true
---

### 软件下载

[百度云](http://pan.baidu.com/s/1jHP25ym)

### phpStorm 安装

这里提供的是 PhpStorm-9.0.exe 的安装包，key：

```
PHPStorm
alfred
===== LICENSE BEGIN =====
35289-12042010
00001Y2rCQfRPrBbQxbXEc9q6u!8wI
96NfhpK1pYy8URPTrXI9IBvLeiEJ2V
YmMsNSqoT71VFQX6GPwY9gC0KydFeE
===== LICENSE END =====
```

安装完基本配置：

1.**字体大小调整**

File --> Settings --> Editor --> Colors & Fonts --> Font

先点击 `Save As...` 按钮，随便保存一个，不然不能修改字体大小。


2.**显示行号**

File --> Settings --> Editor --> General --> Appearance

然后将 Show line numbers 打钩。

3.**将编辑的文件加星号标识**

File --> Settings --> Editor --> General --> Editor Tabs

勾选 Mark modified tabs with asterisk.

4.**UTF-8 Setting**

File --> Settings --> Editor --> File Encodings

change all others file encodings to utf-8.

this setting must be restart PhpStorm to take effect.


改个bug：

File --> **Default Settings** --> Editor --> File Encodings

这里要用 **Default Settings** ，不然每次新建一个项目的时候，字符编码就又会变成 GBK。


5.**给.ctp结尾的文件设置为HTML着色**

File --> Settings --> Editor --> File Types


###  wnmp 安装

一直点击下一步就可以了。

这是安装成功之后的图片：

<img src="{{ site.url }}/images/201604/15_01.png" alt="15_01" />


### 建立第一个PHP程序 Hello World

打开 PhpStorm, File-->New Project...

<img src="{{ site.url }}/images/201604/15_02.png" alt="15_02" />

然后新建文件 hello.php

```php
<?php
/**
 * Created by PhpStorm.
 * User: JIAN.HUANG
 * Date: 2016/4/15
 * Time: 15:12
 */

echo "hello world";

?>
```

**开启 wnmp**

修改 nginx.conf 配置文件

<img src="{{ site.url }}/images/201604/15_03.png" alt="15_03" />

按照上图打开 nginx.conf 配置文件。

```
# Begin HTTP Server
server {
    listen 80; # IPv4
    server_name localhost;

    ## Parametrization using hostname of access and log filenames.
    access_log logs/localhost_access.log;
    error_log logs/localhost_error.log;

    ## Root and index files.
    root html;
    index  index.php index.html index.htm;

    ## If no favicon exists return a 204 (no content error).
    location = /favicon.ico {
        try_files $uri =204;
        log_not_found off;
        access_log off;
    }
    ...
    ...
}
```

找到这段配置信息。将 `root html` 中的 `html` 替换成你自己 helloWorld 项目的路径。修改如下：

`root E:\PHP\Code\HelloWorld;`

最后，在浏览器中输入

http://localhost/hello.php

在界面上看到 hello world 了，就说明成功了。


**注意：**

如果改为 `root E:\PHP\Code;`

浏览器中输入  http://localhost/HelloWorld/hello.php 也是可以的，但是不要这么使用，有的时候会有问题。
