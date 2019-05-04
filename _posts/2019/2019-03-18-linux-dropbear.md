---
layout: post
title: "[Linux] dropbear"
date: 2019-03-18
author: mdgsf
comments: true
categories: Linux
tags: [Linux]
description: ""
published: true #default true
---

### 安装 dropbear

```
sudo apt-get install dropbear
```

### 连接设备

```
dbclient -i id_rsa.db root@192.168.0.100
```

### 传送电脑上的文件到设备

```
dbcp -i id_rsa.db ~/test.txt root@192.168.0.100:~
```

### 传送设备上的文件到电脑

```
dbcp -i id_rsa.db root@192.168.0.233:/home/root/test.txt .
```
