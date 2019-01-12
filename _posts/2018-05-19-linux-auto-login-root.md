---
layout: post
title:  "[linux] root用户自动登录"
date:   2018-05-19
comments: true
categories: linux
tags: linux, apt
description:
published: true
---


### 1.设置 root 用户密码

sudo passwd root

### 2. /etc/lightdm/lightdm.conf

```
[Seat:*]
autologin-guest=false
autologin-user=root
autologin-uesr-timeout=0
greeter-session=lightdm-gtk-greeter
```

### 3. /root/.profile

将

```
mesg n || true
```

改为

```
tty -s && mesg n || true
```

重启电脑
