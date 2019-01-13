---
layout: post
title: "Ubuntu setting"
date: 2016-02-28
author: mdgsf
comments: true
categories: Linux
tags: ubuntu, vbox
description: "After install ubuntu in vbox, do some common setting"
published: true #default true
---


## 1.Install vBoxAddition 

### 1.1

<img src="{{ site.url }}/images/201602/28_01.jpg" alt="28_01" />

### 1.2 

If there is no **VBoxGuestAddition.iso**, go to the vbox installed directory.

<img src="{{ site.url }}/images/201602/28_02.jpg" alt="28_02" />


## 2.Set share directory between local host and vbox

### 2.1

<img src="{{ site.url }}/images/201602/28_03.png" alt="28_03" />

### 2.2 

click the Auto-mount and fixed allocation.

<img src="{{ site.url }}/images/201602/28_04.png" alt="28_04" />

### 2.3

Remember to restart the ubuntu, or maybe it can't be used.

### 2.4 mount command

sudo mkdir /mnt/shared

sudo mount -t vboxsf share /mnt/shared



## 3.Get the root 

sudo passwd root

Enter new UNIX password: 

Retype new UNIX password:

passwd: password updated successfully

And after that, if you want to get root, do as follows:

su root

Password:


## 4.Install vim
apt-get install vim


## 5.Network setting

### 5.1

sudo vim /etc/network/interfaces

```
auto lo 
iface lo inet loopback
auto eth0
iface eht0 inet static
address 172.17.92.238
netmask 255.255.255.0
gateway 172.17.92.110
```

### 5.2

```
sudo vim /etc/resolv.conf
nameserver 114.114.114.114
nameserver 8.8.8.8
```

### 5.3 select bridged networking

<img src="{{ site.url }}/images/201602/28_05.jpg" alt="28_05" />

### 5.4 

sudo /etc/init.d/networking restart


## 6.terminal setting

```
vim ~/.bashrc
alias ls='ls --color=auto'
alias f='fg'
alias j='jobs'
```

<img src="{{ site.url }}/images/201602/28_06.png" alt="28_06" />

source ~/.bashrc


## 7.Install software

apt-get update

apt-get upgrade

apt-get install g++

apt-get install gdb

```
apt-get install openssh-server
sudo service ssh start (must use sudo even you are root)
ps -e | grep sshd
```

apt-get install ctags

apt-get install samba

apt-get install git

apt-get install subversion


