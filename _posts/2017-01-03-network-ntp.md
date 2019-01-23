---
layout: post
title: "[Network][NTP] 网络时间同步技术"
date: 2017-01-08
author: mdgsf
comments: true
categories: Network
tags: [Network,Ntp]
description:
published: true #default true
---

## NTP

NTP（Network Time Protocol）从1985年诞生来，目前仍在在大部分的计算机网络中起着同步系统时间的作用。


### 基本原理

服务器和客户端之间通过二次报文交换，确定主从时间误差，客户端校准本地计算机时间，完成时间同步，有条件的话进一步校准本地时钟频率。

### 时间同步过程

服务器在UDP的132端口提供授时服务，客户端发送附带T1时间戳（Timestamp）的查询报文给服务器，
服务器在该报文上添加到达时刻T2和响应报文发送时刻T3，客户端记录响应报到达时刻T4。

<img src="{{ site.url }}/images/2017/01/03_00.png" alt="03_00" />


### 时差计算

这个图中用蓝色标注了主从直接来回链路的时延Sigma：

Sigma = (t4-t1)-(t3-t2)

因此，假设来回网络链路是对称的，即传输时延相等，那么可以计算客户端与服务器之间的时间误差Delta为：

Delta = t2-t1-Sigma/2=((t2-t1)+(t3-t4))/2

客户端调整自身的时间Delta，即可完成一次时间同步。


> 其实很好理解，你只要想一下，如果client和server的时间本来就是同步好的。
> 那么 t1 + Sigma/2 == t2，这个式子的意思是，
> 数据包从client发送时出发的时间，加上数据包在网络中传输到server的时间，
> 就应该等于数据包到达server时，server的时间。

```
t1 = 231 , t2 = 135 , t3 = 137 , t4 = 298
Sigma = (t4-t1) - (t3-t2) = 65
Delta = t2 - t1 - Sigma/2 = -128.5
```

t4-t1 : 就是client从发出数据包一直到收到数据包的时间。

t3-t2 ：就是server处理这个消息的时间。

Sigma : 即为数据包在网络中来回的时间。

Sigma/2 : 就是数据包从client到server的时间，或者说是从server到client的时间，这是一个平均值。

Delta : 就是client和server的时间差值。是个负数，说明client的时间快了。是正数，说明client时间慢了。等于零，说明client和server的时间已经是同步的了。



### 误差分析

局域网内计算机利用NTP协议进行时间同步，时间同步精度在5ms左右，主要误差包括：

1）计算机打时间戳的位置在应用层，受协议栈缓存、任务调度等影响，不能在网络报文到来时马上打戳；

2）各种中间网络传输设备带来的传输时延不确定性以及链路的不对称性，将进一步降低NTP时间同步精度。



### 参考链接

[https://segmentfault.com/a/1190000005337116](https://segmentfault.com/a/1190000005337116)





