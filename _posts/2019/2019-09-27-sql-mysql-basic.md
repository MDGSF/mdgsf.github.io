---
layout: post
title: "[MySQL] 命令行基础操作"
date: 2019-09-27
comments: true
categories: 数据库
tags: [Sql,MySQL]
description:
published: true
---

### 查看帮助文档

```
mysql --help
```

### 连接服务器

```
mysql -h 127.0.0.1 -u root -P 3306 -p
```

```
-h: 指定 IP 地址
-u: 指定用户
-P: 指定端口（这个是大写的 P）
-p: 指定要输入密码，这个后面不需要跟参数
```

### 查看有哪些数据库

```
show databases;
```

### 指定使用哪个数据库

```
use <数据库名>;
```

```
mysql> use information_schema;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
```

### 查看当前数据库中的所有表格

```
show tables;
```

### 查看当前使用的是哪个数据库

```
mysql> select database();
+--------------------+
| database()         |
+--------------------+
| information_schema |
+--------------------+
1 row in set (0.00 sec)
```

### 查看当前数据库版本

```
mysql> select version();
+-------------------------+
| version()               |
+-------------------------+
| 5.7.27-0ubuntu0.16.04.1 |
+-------------------------+
1 row in set (0.01 sec)
```

### 查看当前时间

```
mysql> select now();
+---------------------+
| now()               |
+---------------------+
| 2019-09-27 10:19:47 |
+---------------------+
1 row in set (0.00 sec)
```

### 查看当前日期

```
mysql> select CURRENT_DATE;
+--------------+
| CURRENT_DATE |
+--------------+
| 2019-09-27   |
+--------------+
1 row in set (0.00 sec)

mysql> select YEAR(CURRENT_DATE);
+--------------------+
| YEAR(CURRENT_DATE) |
+--------------------+
|               2019 |
+--------------------+
1 row in set (0.00 sec)

mysql> select MONTH(CURRENT_DATE);
+---------------------+
| MONTH(CURRENT_DATE) |
+---------------------+
|                   9 |
+---------------------+
1 row in set (0.00 sec)

mysql> select DAYOFMONTH(CURRENT_DATE);
+--------------------------+
| DAYOFMONTH(CURRENT_DATE) |
+--------------------------+
|                       27 |
+--------------------------+
1 row in set (0.00 sec)
```

