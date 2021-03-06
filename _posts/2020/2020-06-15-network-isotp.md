---
layout: post
title: "[Network] can、isotp 和 isotptun"
date: 2020-06-15
author: mdgsf
comments: true
categories: Network
tags: [Network, can, isotp]
description:
published: true #default true
---

- [背景介绍](#背景介绍)
- [CAN 协议介绍](#can-协议介绍)
- [ISO-TP 协议介绍](#iso-tp-协议介绍)
  - [ISO-TP 协议发送数据流程图](#iso-tp-协议发送数据流程图)
  - [ISO-TP 协议帧详细设计](#iso-tp-协议帧详细设计)
    - [Single Frame(SF)](#single-framesf)
    - [First Frame(FF)](#first-frameff)
    - [Consecutive Frame(CF)](#consecutive-framecf)
    - [Flow Control Frame(FC)](#flow-control-framefc)
  - [ISO-TP 协议对丢包的处理](#iso-tp-协议对丢包的处理)
- [isotptun 介绍](#isotptun-介绍)
- [相关资源](#相关资源)

## 背景介绍

**CAN** 是控制器局域网络（Controller Area Network, CAN）的简称，是由以研发和生产汽车电子产品著称的德国 BOSCH 公司开发的，并最终成为国际标准（ISO 11898），是国际上应用最广泛的现场总线之一。CAN 传输协议提供点对点的通信，在两个 CAN 节点之间通过 `CAN ID` 来进行通信。如下图所示：

![15_03.png]({{ site.url }}/images/2020/06/15_03.png)

**ISO-TP 协议**是一个基于 **CAN 协议**的请求应答协议。**ISO-TP 协议**是一个不可靠的数据报协议。因此，不支持错误提示，例如：“由于错误的序列号导致了数据包丢失”是没有任何提示的。**ISO-TP 协议**示意图如下：

![15_04.png]({{ site.url }}/images/2020/06/15_04.png)

**isotptun** 是基于 **ISO-TP 协议**的双向 IP 隧道。示意图如下：

![15_05.png]({{ site.url }}/images/2020/06/15_05.png)

## CAN 协议介绍

在 Linux 下的 `/usr/include/linux/can.h` 文件下，可以看到定义的 CAN 帧的结构体：

```cpp
struct can_frame {
    u32   can_id;
    u8    can_dlc;
    u8    __pad;   /* padding */
    u8    __res0;  /* reserved / padding */
    u8    __res1;  /* reserved / padding */
    u8    data[8];
};
```

- **can_id**：是 CAN 帧的标识符。
- **can_dlc**：是这个 CAN 帧数据的长度，就是 data 字段中保存了几个字节的数据。
- **data**：就是这个 CAN 帧携带的数据，最多只能携带 8 个字节。
- **其他**：其他 3 个字段目前没有用到。

通过上面的结构体，我们可以看到 CAN 帧的结构体非常简单，可以携带的数据量也是少的惊人，只有 8 个字节。
而我们却要在这 8 个字节上面实现一个请求应答协议，真是难以想象。像 TCP 这种复杂的请求应答协议，单单一个头部都至少有 20 个字节的长度。

## ISO-TP 协议介绍

**ISO-TP 协议** 就是在 **CAN 协议**上面实现的请求应答协议，**ISO-TP 协议**用一个字节来作为头部，剩下的用来传输数据。**ISO-TP 协议**一共定义了 4 种帧的类型。

- **Single Frame**：`单帧`，发送的数据在一个 CAN 帧里面就可以放得下的时候，可以直接使用`单帧`。
- **First Frame**：`首帧`，发送的数据在一个 CAN 帧里面放不下，需要拆分为多个 CAN 帧时，先发送一个`首帧`。
- **Consecutive Frame**：`连续帧`，在发送完`首帧`之后，发送`连续帧`。
- **Flow Control**：`流控帧`，在收到对方的`首帧`之后，回应一个`流控帧`，对方在收到`流控帧`之后才能发送`连续帧`。

因为 **ISO-TP 协议**是基于 **CAN 协议**的，所以一共就只有 8 个字节可以使用，**ISO-TP 协议**是使用第一个字节的前面 4 个比特来表示帧的类型。如下图所示：

|PCI |function         |nibble | bit value |
|----|-----------------|-------|-----------|
| SF |Single Frame     | 0     | 0000xxxx  |
| FF |First Frame      | 1     | 0001xxxx  |
| CF |Consecutive Frame| 2     | 0010xxxx  |
| FC |Flow Control     | 3     | 0011xxxx  |

因为每一个 CAN 帧都至少使用了一个字节来标识类型，所以基于 8 个字节的 **CAN 协议**的 **ISO-TP 协议**开销至少是 `1/8 = 12.5%`。有一种叫 `CAN FD` 的扩展 **CAN 协议**，可以携带高达 64 字节的数据，如果 **ISO-TP 协议**是基于 `CAN FD` 的话，那协议的开销就是 `1/64 = 1.6%`。但是很可惜，windows 下的第三方驱动只支持 8 个字节的经典 **CAN 协议**。

### ISO-TP 协议发送数据流程图

**ISO-TP 协议**提供了分段、带流控的数据传输、重组这些功能。**ISO-TP 协议**的主要作用是能够传输大于单个 CAN 帧能够传输的数据。大于单个 CAN 帧的数据会被拆分为多个部分（**分段**），每个部分都能够通过单个 CAN 帧来传输，接收端对收到的数据进行重组。

下图展示了未被分段的数据传输。

![15_01.png]({{ site.url }}/images/2020/06/15_01.png)

下图展示了分段的数据传输。

![15_02.png]({{ site.url }}/images/2020/06/15_02.png)

### ISO-TP 协议帧详细设计

下面我们来看下 **ISO-TP 协议** 4 个帧的详细设计：

| PCI |   B[0]    |   B[1]    | B[2] | B[3] | B[4] | B[5] | B[6] | B[7] |
|-----|-----------|-----------|------|------|------|------|------|------|
| SF  | 0000 LLLL | data      | data | data | data | data | data | data |
| FF  | 0001 LLLL | LLLLLLLL  | data | data | data | data | data | data |
| CF  | 0010 NNNN | data      | data | data | data | data | data | data |
| FC  | 0011 FFFF | Blocksize | STm  | n.a. | n.a. | n.a. | n.a. | n.a. |

- **LLLL**：PDU（Protocol Data Unit） 的长度信息。
- **NNNN**：`连续帧`中的序列号，一共只有 4 比特，所以数据范围是 `0 ~ 15`。
- **FFFF**：流控状态信息。
- **Blocksize**：`0 ~ 15` （0 = disabled）
- **STm**：Separation Time minimum，最小发送间隔。
- **data**：PDU payload data，上层要发送的数据。
- **n.a.**：未分配。
- **B[x]**：CAN 帧中的第 x 个字节。

#### Single Frame(SF)

**SF** 的帧标识是第一个字节的前面 4 个比特，是 0。

**SF** 的第一个字节后面 4 个比特表示的是帧的数据长度，最大可以表示 `2^4 - 1 = 15` 字节。一个 CAN 帧才 8 个字节，首部用去了一个字节，所以 **SF** 最多只能发送 7 个字节的数据。

#### First Frame(FF)

**FF** 的帧标识是第一个字节的前面 4 个比特，是 1。

**FF** 是用第一个字节的后面 4 个比特和第二个字节合起来表示整个 PDU 的数据长度，最大可以表示 `2^12 - 1 = 4095` 字节。这说明了 **ISO-TP 协议** 给上层提供的接口中，最大能够发送的数据长度就是 4095，如果超过了这个长度，上层就需要自己来分段。

不过 **ISO-TP 协议** 这里提供了一种增强版的实现。把表示数据长度的这 12 比特全部置为 0，然后用接下来的 4 个字节来表示整个 PDU 的数据长度，最大可以表示 `2^32 - 1  字节（~4GB）`。

#### Consecutive Frame(CF)

**CF** 的帧标识是第一个字节的前面 4 个比特，是 2。

**CF** 的第一个字节的后面 4 个比特，表示的是序列号，范围是 `0 ~ 15`，从 0 开始一直增加到 15，然后再次从 0 开始。

**CF** 没有表示数据长度的字段的，除了第一个字节之外，剩下的都是数据。只有最后一个 **CF** 的数据才可能没有填充满。

#### Flow Control Frame(FC)

**FC** 的帧标识是第一个字节的前面 4 个比特，是 3。

### ISO-TP 协议对丢包的处理

**CAN 协议**本身存在丢包的可能，在测试的过程中，如果大量发送 CAN 帧，丢包几乎就是必现的情况。所以 **ISO-TP** 协议里面加入了序列号来处理丢包的情况。

在`连续帧`中，第一个字节的后面 4 个比特，就是表示序列号，范围是 `0 ~ 15`，当序列号达到 15 之后就又从 0 开始。在接收端会检查这个序列号是否正确，如果发现错误了，接收端会把之前已经读取到的数据清空，并等待下一个`首帧`的到来。也就是说接下来收到的所有`连续帧`都会被丢弃。

**总结**：一个 PDU 可以被拆分为多个 CAN 帧，一旦出现 CAN 帧丢失，整个 PDU 就会被丢弃。

## isotptun 介绍

**isotptun** 是基于 **ISO-TP 协议**的双向 IP 隧道。**isotptun** 启用之后，两个端点之间就可以直接通过 IP 来通信了。那这是怎么实现的呢？

假定我们有如下模块：

- `host1`, `虚拟网卡1`, `isotptun1`, `isotp模块1`, `can模块1`
- `host2`, `虚拟网卡2`, `isotptun2`, `isotp模块2`, `can模块2`

一共涉及两台主机 `host1` 和 `host2`。

`isotptun1` 在 `host1` 上面创建 `虚拟网卡1`。

`isotptun2` 在 `host2` 上面创建 `虚拟网卡2`。

`isotptun1` 把从 `虚拟网卡1` 读取到的数据写入 `isotp模块1`。

`isotptun1` 把从 `isotp模块1` 读取到的数据写入 `虚拟网卡1`。

`isotptun2` 把从 `虚拟网卡2` 读取到的数据写入 `isotp模块2`。

`isotptun2` 把从 `isotp模块2` 读取到的数据写入 `虚拟网卡2`。

`isotp模块1` 和 `isotp模块2` 的数据最终会通过 `can模块1` 和 `can模块2` 进入 `CAN bus` 总线。通过 `CAN ID` 来找到对方。

虚拟网卡创建之后，可以给 `虚拟网卡1` 分配一个 IP 地址 `192.168.1.1`，给 `虚拟网卡2` 分配一个 IP 地址 `192.168.1.2`。当然，你也可以分配其他的 IP 地址，只要两个主机是在同一个网段内就可以。这样，两个主机就像是在同一个局域网内。

**缺陷**：

1. 由于 **CAN 协议**本身速度较慢，所以在测试时，速度一般也就只能达到十几 KBytes/s。
2. **CAN 协议**这里存在丢包的可能，所以一旦丢包，会导致 TCP 大量重传。

**问题：上面说 ISO-TP 协议会丢包，那为什么可以用来建立 IP 隧道？**

**答：**只要上层使用基于 TCP 的协议，TCP 本身会重传，所以没有问题。**ISO-TP 协议** 可以认为是一个类似于数据链路层的协议。那如果你用的是 UDP 协议的话，那数据就是有可能出现丢失。

## 相关资源

- ISO 15765-2.pdf
- [hartkopp/can-isotp]
- [linux-can/can-utils]

[hartkopp/can-isotp]: https://github.com/hartkopp/can-isotp
[linux-can/can-utils]: https://github.com/linux-can/can-utils
