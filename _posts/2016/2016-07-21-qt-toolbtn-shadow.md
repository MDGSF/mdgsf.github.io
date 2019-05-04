---
layout: post
title:  "[Qt] 给toolbutton增加阴影效果"
date:   2016-07-21
comments: true
categories: Qt
tags: [C++,Qt]
description:
published: true
---

```
QGraphicsDropShadowEffect *wndShadow = new QGraphicsDropShadowEffect;
wndShadow->setBlurRadius(9.0);
wndShadow->setColor(QColor(0, 0, 0, 160));
wndShadow->setOffset(4.0);
ui->localState_toolButton->setGraphicsEffect(wndShadow);
```
