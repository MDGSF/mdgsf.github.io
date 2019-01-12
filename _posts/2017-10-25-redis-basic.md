---
layout: post
title:  "[Redis] 基础使用"
date:   2017-10-25
comments: true
categories: Redis
tags: redis
description: ""
published: true
---


## 启动redis服务器

redis-server.exe redis.windows.conf

默认为127.0.0.1:6379


## 客户端连接

redis-cli -h host -p port -a password

redis-cli.exe -h 127.0.0.1 -p 6379

可简写为redis-cli


## 获取redis服务器的配置信息

config get * : 获取所有的信息

config get requirepass : 获取密码

config get dir : 获取redis的目录


## 设置密码

config get requirepass

设置密码: config set requirepass "123456"

auth "123456"

取消密码: config set requirepass ""


## 备份数据

save : 该命名将在redis目录下创建dump.rdb文件。

bgsave: 该命令将在后台执行。

如果需要恢复数据，只需将备份文件 (dump.rdb) 移动到 redis 安装目录并重新启动服务即可。


## 哈希表

**添加，查看**

```
127.0.0.1:6379> hset runoobkey name "huangjian"
(integer) 0
127.0.0.1:6379> hget runoobkey name
"huangjian"
```

**同时添加多个**

```
127.0.0.1:6379> hmset runoobkey name "jian" age "123"
OK
127.0.0.1:6379> hgetall runoobkey
1) "name"
2) "jian"
3) "age"
4) "123"
127.0.0.1:6379> hget runoobkey name
"jian"
127.0.0.1:6379> hget runoobkey age
"123"
```

**删除**

```
127.0.0.1:6379> hdel runoobkey age
(integer) 1
127.0.0.1:6379> hgetall runoobkey
1) "name"
2) "huangjian"
127.0.0.1:6379> hdel runoobkey age
(integer) 0
```

**判断键是否存在**

```
127.0.0.1:6379> hexists runoobkey age
(integer) 0
127.0.0.1:6379> hexists runoobkey name
(integer) 1
```

## 发布订阅模式

在客户端1订阅: subscribe mychannel

在客户端2发布消息: publish mychannel "Redis is great"

然后客户端1就可以收到。


## 查看所有数据

redis-cli keys '*'