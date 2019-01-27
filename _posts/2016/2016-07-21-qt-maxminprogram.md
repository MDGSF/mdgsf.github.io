---
layout: post
title:  "[Qt] 点击任务栏最大化最小化程序"
date:   2016-07-21
comments: true
categories: Qt
tags: [C++,Qt]
description:
published: true
---


```
//let the window icon in taskbar when clicked, it can max or min the window
this->setWindowFlags(Qt::Window|Qt::FramelessWindowHint
    |Qt::WindowSystemMenuHint|Qt::WindowMinimizeButtonHint|Qt::WindowMaximizeButtonHint);
```
