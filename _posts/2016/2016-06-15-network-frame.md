---
layout: post
title: "[Network] Frame in data link layer"
date: 2016-06-15
author: mdgsf
comments: true
categories: Network
tags: [Network,Frame]
description:
published: true #default true
---

Black list: 只要Source Mac在list中，就不转发。

White list: 只有Source Mac在list中，才转发。

<hr />

Frame 的格式：

```

|       6字节     |      6字节       |  2字节    |        46-->1500字节           |  4字节  |
|     目的地址     |      源地址      | 长度/类型 |            数据或填充           |  CRC   |

|<------------------min:64字节(512bit), max=1518字节(12144bit) --------------------------->|

```

Mac 地址格式：

<img src="{{ site.url }}/images/201606/15_01.png" alt="15_01" />

<hr />

源地址始终是单播地址，因为任何帧都只可能来自一个站。

而目的地址则有可能是单播，多播或者广播地址。

1. 单播地址：目的地址的第一个字节的最低位是0 。(例如： 4A:30:10:21:10:1A)

2. 多播地址：目的地址的第一个字节的最低位不是0 。(例如： 47:20:1B:2E:08:EE)

3. 广播地址：目的地址的 6 个字节全都是1 。(例如： FF:FF:FF:FF:FF:FF)
