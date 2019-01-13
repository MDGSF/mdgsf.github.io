---
layout: post
title: "[Nginx] 路径问题"
date: 2018-11-21
author: mdgsf
comments: true
categories: nginx
tags: nginx
description:
published: true #default true
---


```
location /api {
    proxy_set_header X-Real-IP $remote_addr;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header Host $http_host;
    proxy_set_header X-Nginx-Proxy true;
    proxy_set_header Connection "";
    proxy_http_version 1.1;
    proxy_pass http://application_server_addr;
    client_max_body_size 10240M;
}
```

请求 http://127.0.0.1/api/v1/xxx

application_server_addr 那里会收到 /api/v1/xxx

如果将

```
proxy_pass http://application_server_addr;
```

修改为 

```
proxy_pass http://application_server_addr/;
``` 

的话（就是在末尾加个斜杠）。

那 application_server_addr 那里会收到 /v1/xxx 。

