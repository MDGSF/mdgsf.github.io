---
layout: post
title: "[Nginx] invalid PID number in /run/nginx.pid"
date: 2018-11-21
author: mdgsf
comments: true
categories: nginx
tags: [Nginx]
description:
published: true #default true
---

```
ps -ef | grep nginx 
```

会看到 nginx 的 master 进程不见了，就只有 worker 进程了。

```
killall nginx

nginx -c /etc/nginx/nginx.conf

nginx -s reload
```
