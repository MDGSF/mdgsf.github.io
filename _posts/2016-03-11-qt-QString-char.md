---
layout: post
title:  "[Qt] QString 和 char* 转换"
date:   2016-03-11
comments: true
categories: Qt
tags: qt, QString
description:
published: true
---



### (1) QString 转 char*

```cpp
char acResult[10240];
//QByteArray baResult = strResult.toLatin1();
QByteArray baResult = strResult.toLocal8Bit();
char *pcResult = baResult.data();
strcpy(acResult, pcResult);
```

### (2) char* 转QString

```cpp
char acName[] = "huang";
QString strName = QString(QLatin1String(acName));
QString strName = QString::fromUtf8(acName);
```