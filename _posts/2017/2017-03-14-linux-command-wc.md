---
layout: post
title:  "[Linux][常用命令] wc"
date:   2017-03-14
comments: true
categories: Linux
tags: [Ls]
description:
published: true
---


### wc 显示 行数，字数，字节数

```
mdgsf@mdgsf-ThinkPad-X260:~/svn/AStyle$ ls
build  doc  file  LICENSE.txt  README.txt  src
mdgsf@mdgsf-ThinkPad-X260:~/svn/AStyle$ wc LICENSE.txt 
  21  173 1100 LICENSE.txt
mdgsf@mdgsf-ThinkPad-X260:~/svn/AStyle$ wc -l LICENSE.txt 
21 LICENSE.txt
mdgsf@mdgsf-ThinkPad-X260:~/svn/AStyle$ wc -w LICENSE.txt 
173 LICENSE.txt
mdgsf@mdgsf-ThinkPad-X260:~/svn/AStyle$ wc -c LICENSE.txt 
1100 LICENSE.txt
mdgsf@mdgsf-ThinkPad-X260:~/svn/AStyle$ wc -m LICENSE.txt 
1100 LICENSE.txt
```


### wc 统计多个文件

```
mdgsf@mdgsf-ThinkPad-X260:~/svn/AStyle$ wc LICENSE.txt README.txt 
  21  173 1100 LICENSE.txt
   9   45  347 README.txt
  30  218 1447 总用量
```


### wc 帮助文档

```
root@mdgsf-ThinkPad-X260:~# wc --help
用法：wc [选项]... [文件]...
　或：wc [选项]... --files0-from=F
Print newline, word, and byte counts for each FILE, and a total line if
more than one FILE is specified.  A word is a non-zero-length sequence of
characters delimited by white space.

如果没有指定文件，或者文件为"-"，则从标准输入读取。

The options below may be used to select which counts are printed, always in
the following order: newline, word, character, byte, maximum line length.
  -c, --bytes            print the byte counts
  -m, --chars            print the character counts
  -l, --lines            print the newline counts
      --files0-from=F    read input from the files specified by
                           NUL-terminated names in file F;
                           If F is - then read names from standard input
  -L, --max-line-length  print the maximum display width
  -w, --words            print the word counts
      --help		显示此帮助信息并退出
      --version		显示版本信息并退出
```


### 统计当前目录下有多少个 h头文件

```
mdgsf@mdgsf-ThinkPad-X260:~/svn/AStyle$ find . -name "*.h"
./src/astyle.h
./src/ASLocalizer.h
./src/astyle_main.h
mdgsf@mdgsf-ThinkPad-X260:~/svn/AStyle$ find . -name "*.h" | wc -l
3
```


### 统计当前目录下所有 h头文件中的代码行数

```
mdgsf@mdgsf-ThinkPad-X260:~/svn/AStyle$ find . -name "*.h" | xargs cat | wc -l
1642
```


### 统计当前目录下所有 h头文件中的代码行数，字数，字节数

```
mdgsf@mdgsf-ThinkPad-X260:~/svn/AStyle$ find . -name "*.h" | xargs cat | wc 
   1642    5511   59833
mdgsf@mdgsf-ThinkPad-X260:~/svn/AStyle$ find . -name "*.h" | xargs cat | wc | awk '{print $1}'
1642
mdgsf@mdgsf-ThinkPad-X260:~/svn/AStyle$ find . -name "*.h" | xargs cat | wc | awk '{print $2}'
5511
mdgsf@mdgsf-ThinkPad-X260:~/svn/AStyle$ find . -name "*.h" | xargs cat | wc | awk '{print $3}'
59833
```


### 统计当前目录下文件的个数

```
mdgsf@mdgsf-ThinkPad-X260:~/svn/AStyle$ ls
build  doc  file  LICENSE.txt  README.txt  src
mdgsf@mdgsf-ThinkPad-X260:~/svn/AStyle$ ls -l | wc -l
7
mdgsf@mdgsf-ThinkPad-X260:~/svn/AStyle$ ls -l
总用量 24
drwxr-xr-x 24 root root 4096 2月  12 14:13 build
drwxr-xr-x  2 root root 4096 2月  12 14:13 doc
drwxr-xr-x  2 root root 4096 2月  12 14:13 file
-rw-r--r--  1 root root 1100 2月  12 14:13 LICENSE.txt
-rw-r--r--  1 root root  347 2月  12 14:13 README.txt
drwxr-xr-x  2 root root 4096 2月  12 14:13 src
mdgsf@mdgsf-ThinkPad-X260:~/svn/AStyle$ 
```



