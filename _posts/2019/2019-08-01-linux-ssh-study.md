---
layout: post
title:  "[Linux][SSH] 配置端口转发"
date:   2019-08-01
comments: true
categories: Linux
tags: [Linux,Ssh]
description:
published: true
---

## 查看帮助文档

```
man ssh
man ssh_config
```

## 命令

重启 ssh 服务： `sudo service ssh restart`

常用参数：

```
-n: Redirects stdin from /dev/null. This must be used when ssh is run in the background.
-N: Do not execute a remote command. This is useful for just forwarding ports.
-T: Disable pseudo-terminal allocation.
-f: Requests ssh to go to background just before command execution.
```

## 配置文件

```
~/.ssh/config
/etc/ssh/ssh_config
```

```
Host myhost
HostName 30.121.120.160
User root
Port 8822
IdentityFile /home/huangjian/.ssh/id_rsa
ServerAliveInterval 60
ServerAliveCountMax 525600
TCPKeepAlive yes
```

然后就可以使用 `ssh myhost` 来连接服务器了。

### 心跳机制

`TCPKeepAlive` 是开启 tcp 那一层的心跳。

`ServerAliveInterval` 和 `ServerAliveCountMax` 是 ssh 那一层的心跳机制。

`ServerAliveInterval 60` 是每隔 60 秒发送一个数据包。

`ServerAliveCountMax` 是一共要发送多少个，超过这个数量了连接就会断开。

如果你想要每 60 秒发送一个心跳包，一共保持 10 分钟，那么你可以这么使用：

```
ServerAliveInterval 60
ServerAliveCountMax 10
```

如果你想要每 60 秒发送一个心跳包，一共保持 1 小时，那么你可以这么使用：

```
ServerAliveInterval 60
ServerAliveCountMax 60
```

### LocalForward

LocalForward 是把服务端的端口映射到本地电脑

LocalForward [本地ip地址:本地端口] [服务端服务的ip地址:服务端服务的端口]

例子：

把服务器上的 6379 端口映射到本地的 16379 端口，需要在配置文件中添加

```
LocalForward 127.0.0.1:16379 127.0.0.1:6379
```

命令 `ssh -L 16379:127.0.0.1:6379 user@host` 实现和上面相同的效果。

### RemoteForward

RemoteForward 是把本地的端口映射到服务器上

RemoteForward [服务端ip地址:服务端端口] [本地服务的ip地址:本地服务的端口]

RemoteForward 需要在服务器的配置文件 /etc/ssh/sshd_config 上开启 `GatewayPorts yes`

例子：

把本地的 8666 端口映射到服务器上的 18666 端口，需要在配置文件中添加

```
RemoteForward 127.0.0.1:18666 127.0.0.1:8666
```

命令 `ssh -R 18666:127.0.0.1:8666 user@host` 实现和上面相同的效果。

## autossh

### example 1

映射 biao 这个服务器上的端口 6379 到本地的 16379 端口。并且 autossh 会在后台监控 ssh 是否正常运行。

```
autossh -M 0 -f -nNTf -L 16379:127.0.0.1:6379 user@biao
```

### example 2

把本地的 6000 端口映射到服务器上的 16000 端口。并且 autossh 会在后台监控 ssh 是否正常运行。

```
autossh -M 0 -f -nNTf -R 16000:127.0.0.1:6000 user@biao
```
