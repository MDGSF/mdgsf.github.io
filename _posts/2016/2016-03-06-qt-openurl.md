---
layout: post
title:  "[Qt] 打开网页"
date:   2016-03-06
comments: true
categories: Qt
tags: [C++,Qt]
description: "qt 打开网页的两种方法"
published: true
---


### 1.用QLabel

```cpp
label->setText(tr("<style>a{text-decoration: none}</style><a style='color: #09A3DC;' href=\"http://www.baidu.com\">baidu</a>"));
label->setTextFormat(Qt::RichText);
label->setOpenExternalLinks(true);
```

### 2.用openUrl(要记得加 "http://")

最开始没有加，怎么也打不开，呵呵。

```cpp
const QUrl url(tr("http://www.baidu.com"));
QDesktopServices::openUrl(url);
```
