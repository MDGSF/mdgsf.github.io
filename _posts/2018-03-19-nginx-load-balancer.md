---
layout: post
title: "[Nginx] 负载均衡配置"
date: 2018-03-19
author: mdgsf
comments: true
categories: nginx
tags: nginx
description:
published: true #default true
---

## nginx.conf 文件配置

```
#user  nobody;
worker_processes  1;

error_log /usr/local/nginx/logs/nginx_error.log crit;
pid logs/nginx.pid;

worker_rlimit_nofile 65535;
events {
	use epoll;
    worker_connections 65535;
}

http {
    include       mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    access_log  logs/access.log  main;

    sendfile on;
    tcp_nopush on;
	tcp_nodelay on;
    keepalive_timeout  65;
    gzip on;
	gzip_min_length 1k;
	gzip_buffers 4 16k;

	upstream webservers {
		server 111.230.34.80:12345 weight=10;
		server 111.230.34.80:12346 weight=10;
	}

    server {
        listen 80 default;
        server_name  localhost;

        location /server {
			proxy_pass http://webservers;
            proxy_redirect    off;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header Host $http_host;
        }
   }
}
```

## HTTP server 代码

```go
package main

import (
	"fmt"
	"io"
	"log"
	"net/http"
)

//HelloServer server1
func HelloServer(w http.ResponseWriter, req *http.Request) {
	io.WriteString(w, "server1\n")
}

func main() {
	fmt.Println("vim-go")

	http.HandleFunc("/", HelloServer)
	err := http.ListenAndServe(":12345", nil)
	if err != nil {
		log.Fatal("ListenAndServe: ", err)
	}
}
```

<img src="{{ site.url }}/images/2018/nginx/2018-03-19_loadbalancer1.png" alt="2018-03-19_loadbalancer1.png" />

<img src="{{ site.url }}/images/2018/nginx/2018-03-19_loadbalancer2.png" alt="2018-03-19_loadbalancer2.png" />
