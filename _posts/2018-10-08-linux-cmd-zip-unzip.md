---
layout: post
title:  "[Linux] zip unzip 压缩命令"
date:   2018-10-08
comments: true
categories: Linux
tags: [Linux,Zip,Unzip]
description:
published: true
---

### 安装 zip

```
apt-get install zip
```

### zip 压缩

```
zip -r xxx.zip ./*
```

### 解压 zip 文件到当前目录

```
unzip filename.zip
```

### 解压 zip 文件到指定目录 

```
unzip -d /home/sunny myfile.zip
```
