---
layout: post
title:  "[python] Windows下使用VirtualEnv"
date:   2017-08-29
comments: true
categories: python
tags: [Python,Pip]
description:
published: true
---

## 1

安装virtualenv

```
pip install virtualenv
```

## 2

新建一个目录

```
E:\>mkdir VEnvDir

E:\>cd VEnvDir

E:\VEnvDir>virtualenv env
Using base prefix 'c:\\python34'
New python executable in E:\VEnvDir\env\Scripts\python.exe
Installing setuptools, pip, wheel...done.

E:\VEnvDir>ls
env
```

## 3

启动虚拟环境

```
E:\VEnvDir>ls
env

E:\VEnvDir>cd env

E:\VEnvDir\env>ls
Include  Lib  pip-selfcheck.json  Scripts  tcl

E:\VEnvDir\env>cd Scripts

E:\VEnvDir\env\Scripts>ls
activate      activate.ps1      deactivate.bat    easy_install-3.4.exe  pip3.4.exe  python.exe   wheel.exe
activate.bat  activate_this.py  easy_install.exe  pip.exe               pip3.exe    pythonw.exe

E:\VEnvDir\env\Scripts>activate.bat

(env) E:\VEnvDir\env\Scripts>
```

## 4

退出虚拟环境

```
(env) E:\VEnvDir\env\Scripts>deactivate.bat
E:\VEnvDir\env\Scripts>
```
