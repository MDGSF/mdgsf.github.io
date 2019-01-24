---
layout: post
title:  "[proxy 代理] cow"
date:   2018-12-06
comments: true
categories: miscellanea
tags: [Cow,Proxy]
description:
published: true
---

https://github.com/cyfdecyf/cow

```
local
~/.cow/rc
listen = http://127.0.0.1:7777
proxy = cow://aes-128-cfb:proxy1_password@111.222.333.444:5555

nohup cow &
```

```
proxy1
listen = cow://aes-128-cfb:proxy1_password@0.0.0.0:5555
proxy = ss://aes-256-cfb:proxy2_password@proxy2_ip_addr:443

nohup cow &
```

```
proxy2
run shadowsocks server
```

