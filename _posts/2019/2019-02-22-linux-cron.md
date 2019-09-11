---
layout: post
title: "[Linux] cron 定时任务"
date: 2019-02-22
author: mdgsf
comments: true
categories: Linux
tags: [Linux]
description: ""
published: true #default true
---

### 选择编辑定时任务时使用的编辑器

```
$ select-editor

Select an editor.  To change later, run 'select-editor'.
  1. /bin/ed
  2. /bin/nano        <---- easiest
  3. /usr/bin/code
  4. /usr/bin/vim.basic
  5. /usr/bin/vim.tiny

Choose 1-5 [2]: 4
```

### 编辑定时任务

编辑当前用户的定时任务

```
crontab -e
```

如果当前用户为 huangjian，`crontab -u huangjian -e` 和 `crontab -e` 是一样的。

`sudo crontab -u root -e` 编辑 root 用户的定时任务

编辑好之后，保存了，就生效了。如果要删除某个定时任务，也用 `crontab -e` 这个
命令打开 crontab 文件，删除掉了，再保存了就好了。

### 查看定时任务

```
$ crontab -l
*/1 * * * * /home/huangjian/local/BashUtils/nginx/rotate.sh
```

### 删除掉所有的定时任务

这个命令慎用！！！

```
crontab -r                删除掉当前用户的所有定时任务
sudo crontab -u root -r   删除掉 root 用户的所有定时任务
```

### cron expression

Field name   | Allowed values  | Allowed special characters
----------   | --------------  | --------------------------
Minutes      | 0-59            | * / , -
Hours        | 0-23            | * / , -
Day of month | 1-31            | * / , - ?
Month        | 1-12 or JAN-DEC | * / , -
Day of week  | 0-6 or SUN-SAT  | * / , - ?

### 例子

```
# Field name   | Allowed values  | Allowed special characters
# ----------   | --------------  | --------------------------
# Minutes      | 0-59            | * / , -
# Hours        | 0-23            | * / , -
# Day of month | 1-31            | * / , - ?
# Month        | 1-12 or JAN-DEC | * / , -
# Day of week  | 0-6 or SUN-SAT  | * / , - ?
#
# * 表示匹配所有的值。
#   比如说 Minutes 使用 * 表示每分钟。
#   Hours 使用 * 表示每小时。
#   Month 使用 * 表示每个月。
#
# / 表示范围和间隔。
#   Minutes 使用 3-59/15 表示从第 3 分钟到第 59 分钟，每隔 15 分钟。


# 每天的 0 点，1 点，2 点....时执行命令
0 0 * * * echo `date` >> /home/huangjian/crontab_demo/everyday_24_00.txt
0 1 * * * echo `date` >> /home/huangjian/crontab_demo/everyday_01_00.txt
0 2 * * * echo `date` >> /home/huangjian/crontab_demo/everyday_02_00.txt
0 3 * * * echo `date` >> /home/huangjian/crontab_demo/everyday_03_00.txt
0 4 * * * echo `date` >> /home/huangjian/crontab_demo/everyday_04_00.txt
0 5 * * * echo `date` >> /home/huangjian/crontab_demo/everyday_05_00.txt
0 6 * * * echo `date` >> /home/huangjian/crontab_demo/everyday_06_00.txt
0 7 * * * echo `date` >> /home/huangjian/crontab_demo/everyday_07_00.txt
0 8 * * * echo `date` >> /home/huangjian/crontab_demo/everyday_08_00.txt
0 9 * * * echo `date` >> /home/huangjian/crontab_demo/everyday_09_00.txt
0 10 * * * echo `date` >> /home/huangjian/crontab_demo/everyday_10_00.txt
0 11 * * * echo `date` >> /home/huangjian/crontab_demo/everyday_11_00.txt
0 12 * * * echo `date` >> /home/huangjian/crontab_demo/everyday_12_00.txt
0 13 * * * echo `date` >> /home/huangjian/crontab_demo/everyday_13_00.txt
0 14 * * * echo `date` >> /home/huangjian/crontab_demo/everyday_14_00.txt
0 15 * * * echo `date` >> /home/huangjian/crontab_demo/everyday_15_00.txt
0 16 * * * echo `date` >> /home/huangjian/crontab_demo/everyday_16_00.txt
0 17 * * * echo `date` >> /home/huangjian/crontab_demo/everyday_17_00.txt
0 18 * * * echo `date` >> /home/huangjian/crontab_demo/everyday_18_00.txt
0 19 * * * echo `date` >> /home/huangjian/crontab_demo/everyday_19_00.txt
0 20 * * * echo `date` >> /home/huangjian/crontab_demo/everyday_20_00.txt
0 21 * * * echo `date` >> /home/huangjian/crontab_demo/everyday_21_00.txt
0 22 * * * echo `date` >> /home/huangjian/crontab_demo/everyday_22_00.txt
0 23 * * * echo `date` >> /home/huangjian/crontab_demo/everyday_23_00.txt

# 每天的 7:50 执行命令
50 7 * * * echo `date` >> /home/huangjian/crontab_demo/everyday_07_50.txt

# 每天的 22:50 执行命令
50 22 * * * echo `date` >> /home/huangjian/crontab_demo/everyday_22_50.txt


# 每隔 1 分钟，2 分钟，3 分钟，4 分钟执行命令
*/1 * * * * echo `date` >> /home/huangjian/crontab_demo/every_1_minute.txt
*/2 * * * * echo `date` >> /home/huangjian/crontab_demo/every_2_minute.txt
*/3 * * * * echo `date` >> /home/huangjian/crontab_demo/every_3_minute.txt
*/4 * * * * echo `date` >> /home/huangjian/crontab_demo/every_4_minute.txt
*/5 * * * * echo `date` >> /home/huangjian/crontab_demo/every_5_minute.txt
*/6 * * * * echo `date` >> /home/huangjian/crontab_demo/every_6_minute.txt
*/7 * * * * echo `date` >> /home/huangjian/crontab_demo/every_7_minute.txt

# 每隔 1 分钟执行一次命令
* * * * * echo `date` >> /home/huangjian/crontab_demo/every_1_minute_method1.txt


# 每隔 1 小时，2 小时，3 小时，4 小时，5 小时，6 小时，7 小时执行命令
0 */1 * * * echo `date` >> /home/huangjian/crontab_demo/every_1_hours.txt
0 */2 * * * echo `date` >> /home/huangjian/crontab_demo/every_2_hours.txt
0 */3 * * * echo `date` >> /home/huangjian/crontab_demo/every_3_hours.txt
0 */4 * * * echo `date` >> /home/huangjian/crontab_demo/every_4_hours.txt
0 */5 * * * echo `date` >> /home/huangjian/crontab_demo/every_5_hours.txt
0 */6 * * * echo `date` >> /home/huangjian/crontab_demo/every_6_hours.txt
0 */7 * * * echo `date` >> /home/huangjian/crontab_demo/every_7_hours.txt


# 每小时的第 0 分钟，第 1 分钟，第 2 分钟，第 3 分钟，.... 执行命令
0 * * * * echo `date` >> /home/huangjian/crontab_demo/every_hour_0th.txt
1 * * * * echo `date` >> /home/huangjian/crontab_demo/every_hour_1th.txt
2 * * * * echo `date` >> /home/huangjian/crontab_demo/every_hour_2th.txt
3 * * * * echo `date` >> /home/huangjian/crontab_demo/every_hour_3th.txt
4 * * * * echo `date` >> /home/huangjian/crontab_demo/every_hour_4th.txt
5 * * * * echo `date` >> /home/huangjian/crontab_demo/every_hour_5th.txt
6 * * * * echo `date` >> /home/huangjian/crontab_demo/every_hour_6th.txt
7 * * * * echo `date` >> /home/huangjian/crontab_demo/every_hour_7th.txt


# 每天的 6 点到 12 点之间，每隔 3 小时执行命令
0 6-12/3 * * * echo `date` >> /home/huangjian/crontab_demo/everyday_6-12_hour_every_3_hour.txt

# 每天的 0 点 20 分，2 点 20 分，4 点 20 分，....　执行命令
20 0-23/2 * * * echo `date` >> /home/huangjian/crontab_demo/every_2_hour_20th_minute.txt

# 每月的 1 号，2 号，3 号，4 号，5 号，6 号，7 号执行命令
0 0 1 * * echo `date` >> /home/huangjian/crontab_demo/everymonth_1.txt
0 0 2 * * echo `date` >> /home/huangjian/crontab_demo/everymonth_2.txt
0 0 3 * * echo `date` >> /home/huangjian/crontab_demo/everymonth_3.txt
0 0 4 * * echo `date` >> /home/huangjian/crontab_demo/everymonth_4.txt
0 0 5 * * echo `date` >> /home/huangjian/crontab_demo/everymonth_5.txt
0 0 6 * * echo `date` >> /home/huangjian/crontab_demo/everymonth_6.txt
0 0 7 * * echo `date` >> /home/huangjian/crontab_demo/everymonth_7.txt

# 每月的 1 号和 15 号执行命令
0 0 1,15 * * echo `date` >> /home/huangjian/crontab_demo/everymonth_1_and_15.txt

# 每周的的周一到周五的 3 点钟执行命令
0 3 * * 1-5 echo `date` >> /home/huangjian/crontab_demo/week_1-5_everyday_3th_hour.txt

# 在周六和周天的 00:00 执行命令
0 0 * * 0,6 echo `date` >> /home/huangjian/crontab_demo/week_6-7_00_00.txt

# 在每个月的 1、11、21、31 号，的 6:30 分执行命令
30 6 */10 * * echo `date` >> /home/huangjian/crontab_demo/everymonth_1_11_21_31.txt

# 在 23 点，0 点到 7 点，每隔 1 分钟执行命令
* 23,0-7/1 * * * echo `date` >> /home/huangjian/crontab_demo/hour_0-7_23_every_minute.txt

# 在 23 点，0 点，2 点，4 点，6 点，每隔 1 分钟执行命令
* 23,0-7/2 * * * echo `date` >> /home/huangjian/crontab_demo/hour_0_2_4_6_23_every_minute.txt

# 每天的 3 点到 5 点，17 点到 20 点，每隔 30 分钟执行命令
*/30 3-5,17-20 * * * echo `date` >> /home/huangjian/crontab_demo/hour_3-5_17-20_every_30_minute.txt

# 每年的 8 月 20 号的 00:00 执行命令
0 0 20 8 * echo `date` >> /home/huangjian/crontab_demo/month_8_20th.txt
```

**注意：** 在有的 crontab 上，配置里面如果有空行也会报错，实在是太坑了。

#### 每分钟执行一次 test.sh

```
*/1 * * * * test.sh
```

上面这么写是有问题的，因为要用绝对路径，修改如下：

```
*/1 * * * * /bin/bash /home/huangjian/test.sh
```

### 每分钟输出一个当前时间到 /home/huangjian/foo.txt 文件末尾

```
*/1 * * * * echo `date` >> /home/huangjian/foo.txt
```

#### 每天 00:00 执行一次

```
0 0 * * * test.sh
```

### 相关文章

[go-cron]({{ site.url }}/2018/01/10/go-cron/)

