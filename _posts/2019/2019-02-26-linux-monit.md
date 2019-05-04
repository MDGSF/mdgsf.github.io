---
layout: post
title: "[Linux] monit 监控"
date: 2019-02-26
author: mdgsf
comments: true
categories: Linux
tags: [Linux]
description: ""
published: true #default true
---

https://mmonit.com/monit/

https://mmonit.com/wiki/Monit/ConfigurationExamples

https://mmonit.com/monit/documentation/monit.html

https://mmonit.com/

### ~/.monit.d/directory_bin

```
check directory bin with path /bin
  if failed permission 755 then unmonitor
  if failed uid 0 then unmonitor
  if failed gid 0 then unmonitor
```

### ~/.monit.d/filesystem_sda

```
check filesystem dev_sda with path /dev/sda
  #if failed permission 660 then unmonitor
  #if failed uid "huangjian" then unmonitor
  #if failed gid "root" then unmonitor
  if space usage > 80% for 5 times within 15 cycles then alert
  if space usage > 90% then alert
  if inode usage > 90% then alert
  if read rate > 1 MB/s for 5 cycles then alert
  if read rate > 500 operations/s for 5 cycles then alert
  if write rate > 1 MB/s for 5 cycles then alert
  if write rate > 500 operations/s for 5 cycles then alert
  if service time > 10 milliseconds for 3 times within 5 cycles then alert
```

### ~/.monit.d/network

```
check network enp0s31f6 with interface enp0s31f6
  if failed link then alert
  if changed link then alert
  if saturation > 90% then alert
  if download > 10 MB/s then alert
  if total uploaded > 10 GB in last hour then alert
  if total downloaded > 10 GB in last hour then alert
```

### ~/.monit.d/nginx

```
check file nginxlog with path /home/huangjian/local/openresty/nginx/logs/access.log
  if size > 1 GB then alert
  group nginx

check file nginxpid with path /home/huangjian/local/openresty/nginx/logs/nginx.pid
  if changed timestamp then alert
  if size == 0 then alert
  group nginx

check process nginxbin with pidfile /home/huangjian/local/openresty/nginx/logs/nginx.pid
  if changed pid then alert
  if changed ppid then alert
  if cpu > 60% for 2 cycles then alert
  if cpu > 80% for 5 cycles then alert
  if totalmem > 1024.0 MB for 5 cycles then alert
  if children > 250 then alert
  group nginx
```

### ~/.monit.d/ping_baidu

```
check host baidu with address www.baidu.com
  if failed ping count 5 size 128 with timeout 10 seconds then alert
```

### ~/.monit.d/process_sshd

```
check process sshd with pidfile /var/run/sshd.pid
   start program  "/etc/init.d/ssh start"
   stop program  "/etc/init.d/ssh stop"
   if failed port 22 protocol ssh then restart
```

### ~/.monit.d/system

```
check system $HOST
  if loadavg (5min) > 6 then alert
  if loadavg (15min) > 5 then alert
  if memory usage > 80% for 4 cycles then alert
  if swap usage > 20% for 4 cycles then alert
  # Test the user part of CPU usage
  if cpu usage (user) > 80% for 2 cycles then alert
  # Test the system part of CPU usage
  if cpu usage (system) > 20% for 2 cycles then alert
  # Test the i/o wait part of CPU usage
  if cpu usage (wait) > 80% for 2 cycles then alert
  # Test CPU usage including user, system and wait. Note that
  # multi-core systems can generate 100% per core
  # so total CPU usage can be more than 100%
  if cpu usage > 200% for 4 cycles then alert
```
