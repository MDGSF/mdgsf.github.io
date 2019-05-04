---
layout: post
title: "[Nginx] master 进程不见了"
date: 2019-02-22
author: mdgsf
comments: true
categories: nginx
tags: [Nginx]
description:
published: true #default true
---

master 进程不见了。而且监听的端口也变成了 worker 进程在监听。

```
# ps -ef | grep nginx
root      7215  7180  0 07:46 pts/0    00:00:00 grep --color=auto --exclude-dir=.bzr --exclude-dir=CVS --exclude-dir=.git --exclude-dir=.hg --exclude-dir=.svn nginx
www-data  9824     1  0 Jan11 ?        00:12:10 nginx: worker process
www-data  9825     1  0 Jan11 ?        00:15:01 nginx: worker process

# netstat -ltnp | grep nginx
tcp        0      0 0.0.0.0:8080            0.0.0.0:*               LISTEN      9824/nginx: worker
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      9824/nginx: worker
tcp        0      0 0.0.0.0:443             0.0.0.0:*               LISTEN      9824/nginx: worker
```
