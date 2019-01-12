---
layout: post
title:  "[scp] scp出现stalled的原因和解决方法"
date:   2016-11-15
comments: true
categories: linux
tags: scp
description:
published: true
---

When transferring large files(for example mksysb images) using scp through a firewall, the scp connection stalls.

Cause:

The reason for scp to stall, is because scp greedily grabs as much bandwith of the network as possible when it transfers files,any delay caused by the network switch of the firewall can easily make the TCP connection stalled.

Solution:

There’s a solution to this problem: Add “-l 8192″ to the scp command.

Adding the option “-l 8192″ limits the scp session bandwith up to 8192 Kbit/second, which seems to work safe and fast enough (upto 1 MB/second):

```
scp -l 4096 testfile.txt huangjian@server_ip:~
```
