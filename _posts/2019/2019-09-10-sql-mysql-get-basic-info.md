---
layout: post
title:  "[MySQL] 获取基础信息"
date:   2019-09-10
comments: true
categories: 数据库
tags: [Sql,MySQL]
description:
published: true
---

### 获取 RDS 基础信息

```
select * from information_schema.tables;
```

### 获取所有数据库的大小

```
SELECT
    table_schema,
    SUM((data_length + index_length) / 1024 / 1024) AS MB
FROM
    information_schema.tables
GROUP BY 1;
```

### 获取指定数据库的大小

```
SELECT
    table_schema,
    SUM((data_length + index_length) / 1024 / 1024) AS MB
FROM
    information_schema.tables
WHERE
    table_schema = 'This is your database name'
GROUP BY table_schema;
```

### 获取指定数据库有多少个表格

```
SELECT
    COUNT(*)
FROM
    information_schema.tables
WHERE
    table_schema = 'This is your database name';
```

### 获取指定数据库中每个表格占用空间大小

```
SELECT
    table_schema,
    table_name,
    (data_length + index_length) / 1024 / 1024 AS MB
FROM
    information_schema.tables
WHERE
    table_schema = 'This is your database name';
```

