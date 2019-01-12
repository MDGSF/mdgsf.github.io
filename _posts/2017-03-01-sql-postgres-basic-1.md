---
layout: post
title:  "[PostgreSQL] ubuntu下安装PostgreSQL"
date:   2017-03-01
comments: true
categories: 数据库
tags: sql, postgres
description:
published: true
---

### 常用sql命令

> insert into user_info(name, signup_date, email) values('黄剑', '2017-03-01', '1342042894@qq.com');
> delete from user_info where name = '李四';
> select * from user_info;
> update user_info set name='李四' where name='张三';
> 
> 
> alter table user_tbl rename to user_info;
> alter table user_info add email varchar(40);
> alter table user_info alter column signup_date set not null;
> 
> alter table user_info add id int;
> alter table user_info drop column id;
> 
> ALTER TABLE public.user_info ADD COLUMN id integer;
> ALTER TABLE public.user_info ALTER COLUMN id SET NOT NULL;
> ALTER TABLE public.user_info ALTER COLUMN id SET DEFAULT nextval('user_info_id_seq'::regclass);
> 
> 
> create table t_tb(id serial, phone varchar(200));
> drop table if exists t_tb;


### 安装

sudo apt-get update

sudo apt-get install postgresql

> Creating new cluster 9.5/main ...
>   config /etc/postgresql/9.5/main
>   data   /var/lib/postgresql/9.5/main
>   locale en_US.UTF-8
>   socket /var/run/postgresql
>   port   5432

  
  
### 配置数据库以允许远程连接访问。

安装完成后，默认只能本地才能连接数据库，其他机子访问不了，需要进行配置。

> 修改监听地址
> sudo gedit /etc/postgresql/9.5/main/postgresql.conf 
> 将 #listen_addresses = 'localhost' 的注释去掉并改为 listen_addresses = '*' 
> 
> 修改可访问用户的IP段
> sudo gedit /etc/postgresql/9.5/main/pg_hba.conf 
> 在文件末尾添加： host all all 0.0.0.0 0.0.0.0 md5 ，表示运行任何IP连接
> 
> 重启数据库
> sudo /etc/init.d/postgresql restart



###添加新用户和新数据库

> 法一：使用PostgreSQL客户端psql
> 运行系统用户"postgres"的psql命令，进入客户端：
> 
> sudo -u postgres psql
> 创建用户"xiaozhang"并设置密码：
> 
> postgres=# create user xiaozhang with password '123456';
> 创建数据库exampledb，所有者为xiaozhang：
> 
> postgres=# create database exampledb owner xiaozhang;
> 将exampledb数据库的所有权限赋予xiaozhang，否则xiaozhang只能登录psql，没有任何数据库操作权限：
> 
> grant all privileges on database exampledb to xiaozhang;



sudo -u postgres psql

psql -U dbuser -d exampledb -h 127.0.0.1 -p 5432


> \l：列出所有数据库。
> \d：列出当前数据库的所有表格。
> \d [table_name]：列出某一张表格的结构。
> 
> \password：设置密码
> \q：退出
> \h：查看SQL命令的解释，比如\h select。
> \?：查看psql命令列表。
> \c [database_name]：连接其他数据库。
> \du：列出所有用户。
> \e：打开文本编辑器。
> \conninfo：列出当前数据库和连接的信息。



<a href="http://www.cnblogs.com/z-sm/archive/2016/07/05/5644165.html" target="_blank">http://www.cnblogs.com/z-sm/archive/2016/07/05/5644165.html</a>



select * from dbusers

select * from dbusers where name='dnxdev|zxcvzxcv2015@163.com'

select id from dbusers where name='dnxdev|zxcvzxcv2015@163.com'

select * from vlns

select * from vlns where no_delete=1 and dbuser_id=(select id from dbusers where name='dnxdev|zxcvzxcv2015@163.com')

select id from vlns where no_delete=1 and dbuser_id=(select id from dbusers where name='dnxdev|zxcvzxcv2015@163.com')




```sql
-- Table: public."Persons"

-- DROP TABLE public."Persons";

CREATE TABLE public."Persons"
(
  id integer NOT NULL DEFAULT nextval('"Persons_id_seq"'::regclass),
  "LastName" character varying,
  "FirstName" character varying,
  "Address" character varying,
  "City" character varying,
  CONSTRAINT "Persons_pkey" PRIMARY KEY (id)
)
WITH (
  OIDS=FALSE
);
ALTER TABLE public."Persons"
  OWNER TO huangjian;
  
  
  

select * from "Persons";

清空数据表
truncate table "Persons";

insert into "Persons"("LastName", "FirstName", "Address", "City")
	values('Adams', 'John', 'Oxford Street', 'London');
insert into "Persons"("LastName", "FirstName", "Address", "City")
	values('Bush', 'George', 'Fifth Avenue', 'New York');
insert into "Persons"("LastName", "FirstName", "Address", "City")
	values('Carte', 'Thomas', 'Changan Street', 'Beijing');


select "City" from "Persons";
select distinct "City" from "Persons"; 相同的只出现一次

select * from "Persons" where "City"='Beijing';

```



