---
layout: post
title: "[Nginx] include"
date: 2019-02-20
author: mdgsf
comments: true
categories: nginx
tags: [Nginx]
description:
published: true #default true
---


```
http {
    include /etc/nginx/conf.d/*.conf;
    include /etc/nginx/sites-enabled/*;
}
```
