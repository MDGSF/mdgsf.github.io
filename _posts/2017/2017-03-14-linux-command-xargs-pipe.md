---
layout: post
title:  "[Linux][常用命令] xargs和管道的区别"
date:   2017-03-14
comments: true
categories: Linux
tags: [Ls]
description:
published: true
---

管道：将前面的标准输出当做后面的标准输入。

xargs：将标准输入作为命令的参数。

### 例子 1

```
root@mdgsf-ThinkPad-X260:/home/mdgsf/svn/AStyle# find . -name "*.h" | xargs grep -rn "test"
./src/astyle.h:807: void testForTimeToSplitFormattedLine();
./src/astyle_main.h:369: // virtual functions are mocked in testing
root@mdgsf-ThinkPad-X260:/home/mdgsf/svn/AStyle# find . -name "*.h" | grep -rn "test"
file/clang-chromium.yaml:59:IncludeIsMainRegex: '([-_](test|unittest))?$'
file/clang-google.yaml:59:IncludeIsMainRegex: '([-_](test|unittest))?$'
file/clang-llvm.yaml:55:  - Regex:           '^(<|"(gtest|isl|json)/)'
file/clang-mozilla.yaml:55:  - Regex:           '^(<|"(gtest|isl|json)/)'
file/clang-visualstudio.yaml:59:IncludeIsMainRegex: '([-_](test|unittest))?$'
file/clang-webkit.yaml:55:  - Regex:           '^(<|"(gtest|isl|json)/)'
.....
```

在上面这个例子里，加了xargs的似乎是正常执行了。直接用管道的输出了超级多的
东西，这里用省略号代替了，好像跑到了所有文件里面去查找了。

### 例子 2

```
root@mdgsf-ThinkPad-X260:/home/mdgsf/svn/AStyle# echo fly | cat
fly
root@mdgsf-ThinkPad-X260:/home/mdgsf/svn/AStyle# echo fly | xargs cat
cat: fly: 没有那个文件或目录
```

在这里例子2这里，管道似乎就直接将fly作为一个字符串输出了。而xargs会在
当前目录下去找fly这个文件，因为找不到所以出错了。

那我们接下来就手动建立一个加fly的文件。

### 例子 3

```
root@mdgsf-ThinkPad-X260:/home/mdgsf/svn/AStyle# touch fly
root@mdgsf-ThinkPad-X260:/home/mdgsf/svn/AStyle# echo "test" > fly
root@mdgsf-ThinkPad-X260:/home/mdgsf/svn/AStyle# cat fly
test
root@mdgsf-ThinkPad-X260:/home/mdgsf/svn/AStyle# echo fly | xargs cat
test
```

### 例子 4

```
root@mdgsf-ThinkPad-X260:/home/mdgsf/svn/AStyle# find . -name "fly"
./fly
root@mdgsf-ThinkPad-X260:/home/mdgsf/svn/AStyle# find . -name "fly" | rm -f
root@mdgsf-ThinkPad-X260:/home/mdgsf/svn/AStyle# find . -name "fly"
./fly
root@mdgsf-ThinkPad-X260:/home/mdgsf/svn/AStyle# find . -name "fly" | xargs rm -f
root@mdgsf-ThinkPad-X260:/home/mdgsf/svn/AStyle# find . -name "fly"
```

在例子4这里，直接使用管道，并没有能够成功删除fly这个文件，应该是管道只是将fly作为一个简单的字符串了。
而xargs才会到系统的当前目录下去找fly这个目录。
