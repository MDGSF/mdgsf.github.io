---
layout: post
title: "[Nginx] 使用 goaccess 分析 nginx 日志"
date: 2019-02-21
author: mdgsf
comments: true
categories: nginx
tags: [Nginx]
description:
published: true #default true
---

[https://goaccess.io](https://goaccess.io)

[https://github.com/allinurl/goaccess](https://github.com/allinurl/goaccess)


## 安装

```
$ echo "deb http://deb.goaccess.io/ $(lsb_release -cs) main" | sudo tee -a /etc/apt/sources.list.d/goaccess.list
$ wget -O - https://deb.goaccess.io/gnugpg.key | sudo apt-key add -
$ sudo apt-get update
$ sudo apt-get install goaccess
```

## 使用

### 终端输出

```
goaccess access.log -c
```

### 静态 HTML 输出

```
goaccess access.log -o report.html --log-format=COMBINED
```

### 实时 HTML 输出

```
goaccess access.log -o /var/www/html/report.html --log-format=COMBINED --real-time-html
```

### 实例

```
goaccess /home/huangjian/local/nginx/logs/access.log -o /home/huangjian/local/nginx/www/html/report.html --log-format=COMBINED --real-time-html --addr=localhost --port=7890
```

在 nginx.conf 中，增加以下内容：

```
server {
    listen       7070;
    server_name  localhost;
    location /report.html {
        alias /home/huangjian/local/nginx/www/html/;
        index report.html;
    }
}
```

打开浏览器访问：localhost:7070/report.html

如果是在服务器上，可以用 ssh 把端口 7070 映射到本地电脑。

