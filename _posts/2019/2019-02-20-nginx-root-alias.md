---
layout: post
title: "[Nginx] root 和 alias 的区别"
date: 2019-02-20
author: mdgsf
comments: true
categories: nginx
tags: [Nginx]
description:
published: true #default true
---

### 区别

root 的处理结果是：root 路径 ＋ location 路径

alias 的处理结果是：使用 alias 路径替换 location 路径

还有一个重要的区别是 alias 后面必须要用 “/” 结束，否则会找不到文件的，而 root 则可有可无。

### root 实例

```
location ^~ /a/ {
    root /home/huangjian/local/nginx/www/html;
}
```

如果一个请求的 URI 是 /a/index.html 时，web 服务器将会返回服务器上的 /home/huangjian/local/nginx/www/html/a/index.html 的文件。

### alias 实例

```
location ^~ /b/ {
    alias /home/huangjian/local/nginx/www/html/alias_dir/;
}
```

如果一个请求的 URI 是 /b/index.html 时，web 服务器将会返回服务器上的 /home/huangjian/local/nginx/www/html/alias_dir/index.html 的文件。

