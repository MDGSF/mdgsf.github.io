---
layout: post
title:  "[Linux][常用命令] AWK"
date:   2017-03-02
comments: true
categories: Linux
tags: [Ls]
description:
published: true
---

### 1

<img src="{{ site.url }}/images/2017/03/02_02.png" alt="2017_03_02_02" />

awk工作流程是这样的：读入有'\n'换行符分割的一条记录，然后将记录按指定的域分隔符划分域，填充域，
$0则表示所有域,$1表示第一个域,$n表示第n个域。默认域分隔符是"空白键" 或 "[tab]键",
所以$1表示登录用户，$3表示登录用户ip,以此类推。

### 2 只是显示/etc/passwd的账户

<img src="{{ site.url }}/images/2017/03/02_03.png" alt="2017_03_02_03" />

这种是awk+action的示例，每行都会执行action{print $1}。

-F指定域分隔符为':'。

### 3 只是显示/etc/passwd的账户和账户对应的shell,而账户与shell之间以tab键分割

<img src="{{ site.url }}/images/2017/03/02_04.png" alt="2017_03_02_04" />

### 4 只是显示/etc/passwd的账户和账户对应的shell,而账户与shell之间以逗号分割,而且在所有行添加列名name,shell,在最后一行添加"blue,/bin/nosh"。

```
root@jian-VirtualBox:# cat /etc/passwd | awk -F ':' 'BEGIN{print "name,shell"} {print $1"\t"$7} END{print "blue,/bin/nosh"}'
name,shell
root /bin/bash
daemon /usr/sbin/nologin
bin /usr/sbin/nologin
sys /usr/sbin/nologin
....
mosquitto /bin/bash
postgres /bin/bash
blue,/bin/nosh
```

awk工作流程是这样的：先执行BEGING，然后读取文件，读入有/n换行符分割的一条记录，然后将记录按指定的域分隔符划分域，
填充域，$0则表示所有域,$1表示第一个域,$n表示第n个域,随后开始执行模式所对应的动作action。
接着开始读入第二条记录······直到所有的记录都读完，最后执行END操作。

### 5 搜索/etc/passwd有root关键字的所有行

```
root@jian-VirtualBox:# awk -F: '/root/' /etc/passwd
root:x:0:0:root:/root:/bin/bash
```

### 6 搜索/etc/passwd有root关键字的所有行，并显示对应的shell

```
root@jian-VirtualBox:# awk -F: '/root/{print $7}' /etc/passwd
/bin/bash
```

### 7 awk有许多内置变量用来设置环境信息，这些变量可以被改变，下面给出了最常用的一些变量。

```
ARGC               命令行参数个数
ARGV               命令行参数排列
ENVIRON            支持队列中系统环境变量的使用
FILENAME           awk浏览的文件名
FNR                浏览文件的记录数
FS                 设置输入域分隔符，等价于命令行 -F选项
NF                 浏览记录的域的个数
NR                 已读的记录数
OFS                输出域分隔符
ORS                输出记录分隔符
RS                 控制记录分隔符
```

### 8 统计/etc/passwd:文件名，每行的行号，每行的列数，对应的完整行内容

```
root@jian-VirtualBox:# awk -F ':' '{print "filename:" FILENAME ",linenumber:" NR ",columns:" NF ",linecontent:" $0}' /etc/passwd
filename:/etc/passwd,linenumber:1,columns:7,linecontent:root:x:0:0:root:/root:/bin/bash
filename:/etc/passwd,linenumber:2,columns:7,linecontent:daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
filename:/etc/passwd,linenumber:3,columns:7,linecontent:bin:x:2:2:bin:/bin:/usr/sbin/nologin
filename:/etc/passwd,linenumber:4,columns:7,linecontent:sys:x:3:3:sys:/dev:/usr/sbin/nologin
filename:/etc/passwd,linenumber:5,columns:7,linecontent:sync:x:4:65534:sync:/bin:/bin/sync
```

```
root@jian-VirtualBox:# awk -F ':' '{printf("filename:%10s,linenumber:%s,columns:%s,linecontent:%s\n", FILENAME, NR, NF, $0)}' /etc/passwd
filename:/etc/passwd,linenumber:1,columns:7,linecontent:root:x:0:0:root:/root:/bin/bash
filename:/etc/passwd,linenumber:2,columns:7,linecontent:daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
filename:/etc/passwd,linenumber:3,columns:7,linecontent:bin:x:2:2:bin:/bin:/usr/sbin/nologin
filename:/etc/passwd,linenumber:4,columns:7,linecontent:sys:x:3:3:sys:/dev:/usr/sbin/nologin
filename:/etc/passwd,linenumber:5,columns:7,linecontent:sync:x:4:65534:sync:/bin:/bin/sync
```

### 9 统计/etc/passwd的账户人数

```
root@jian-VirtualBox:# awk 'BEGIN{count=0; print "[start]user count is ", count} {count=count+1;print $0;} END{print "[end]user count is ", count}' /etc/passwd
[start]user count is  0
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
...
ftp:x:122:129:ftp daemon,,,:/srv/ftp:/bin/false
mosquitto:x:1001:1001:mosquitto,2123,3213,321,33123:/home/mosquitto:/bin/bash
postgres:x:123:130:PostgreSQL administrator,,,:/var/lib/postgresql:/bin/bash
[end]user count is  45
```

### 10 统计某个文件夹下的文件占用的字节数

```
root@jian-VirtualBox:# ls -l | awk 'BEGIN{size=0;} {size=size+$5;} END{print "[end]size is ", size}'
[end]size is  55837
root@jian-VirtualBox:# ls -l | awk 'BEGIN{size=0;} {size=size+$5;} END{print "[end]size is ", size/1024}'
[end]size is  54.5283
```

### 11 如果以M为单位显示

```
root@jian-VirtualBox:# ls -l | awk 'BEGIN{size=0;} {size=size+$5;} END{print "[end]size is ", size/1024/1024}'
[end]size is  0.0532503
```

### 12 统计某个文件夹下的文件占用的字节数,过滤4096大小的文件(一般都是文件夹)

```
root@jian-VirtualBox:# ls -l | awk 'BEGIN{size=0;print "[start]size is", size} {if($5!=4096){size=size+$5}} END{print "[end]size is", size/1024/1024, "M"}'
[start]size is 0
[end]size is 1024.23 M
```

### 13 显示/etc/passwd的账户

```
root@jian-VirtualBox:# awk -F ':' 'BEGIN{count=0;} {name[count]=$1;count++;} END{for(i=0;i<NR;i++){print i,name[i]}}' /etc/passwd
0 root
1 daemon
2 bin
3 sys
4 sync
5 games
6 man
```

[http://www.cnblogs.com/ggjucheng/archive/2013/01/13/2858470.html](http://www.cnblogs.com/ggjucheng/archive/2013/01/13/2858470.html)
