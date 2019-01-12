---
layout: post
title:  "[linux] 建立一下新的账号"
date:   2018-11-14
comments: true
categories: linux
tags: linux, new user
description:
published: true
---

删除用户
userdel spark 

添加用户
useradd -r -m -s /bin/bash lyy

设置用户密码
passwd lyy

添加 root 权限
chmod +w /etc/sudoers
vim /etc/sudoers

```
root ALL=(ALL:ALL) ALL
lyy  ALL=(ALL:ALL) ALL
```

切换用户
su lyy

切换到 root
sudo su

