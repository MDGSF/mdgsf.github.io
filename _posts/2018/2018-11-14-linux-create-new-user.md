---
layout: post
title:  "[Linux] 建立一个新的账号"
date:   2018-11-14
comments: true
categories: Linux
tags: [Linux,New user]
description:
published: true
---

### 方法一

删除用户

```
userdel huangjian
```

添加用户

```
useradd -r -m -s /bin/bash huangjian
```

设置用户密码

```
passwd huangjian
```

添加 root 权限

```
chmod +w /etc/sudoers
vim /etc/sudoers
```

```
root ALL=(ALL:ALL) ALL
huangjian  ALL=(ALL:ALL) ALL
```

切换用户

```
su lyy
```

切换到 root

```
sudo su
```

### 方法二

```
adduser username
```

```
usermod -aG sudo username
```
