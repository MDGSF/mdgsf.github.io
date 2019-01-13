---
layout: post
title:  "[Git] git 基础使用"
date:   2017-07-14
comments: true
categories: Git
tags: git
description:
published: true
---


## 一：创建版本库

1. 通过命令 git init 把这个目录变成git可以管理的仓库，如下：

<img src="{{ site.url }}/images/2017/07/14_0.png" alt="14_0" />


2. 把文件添加到版本库中。

第一步：使用命令 git add readme.txt添加到暂存区里面去。如下：

<img src="{{ site.url }}/images/2017/07/14_1.png" alt="14_1" />

第二步：用命令 git commit告诉Git，把文件提交到仓库。

<img src="{{ site.url }}/images/2017/07/14_2.png" alt="14_2" />


3. 修改文件内容。

<img src="{{ site.url }}/images/2017/07/14_3.png" alt="14_3" />


查看具体修改了哪些内容:

<img src="{{ site.url }}/images/2017/07/14_4.png" alt="14_4" />

可以看到我们给reame.txt 文件增加了一行bbb内容。然后我们继续把修改的内容提交到版本库。

<img src="{{ site.url }}/images/2017/07/14_5.png" alt="14_5" />


4. 删除文件。

<img src="{{ site.url }}/images/2017/07/14_12.png" alt="14_12" />


## 二：版本回退

查看提交的日志。

<img src="{{ site.url }}/images/2017/07/14_6.png" alt="14_6" />

上面的信息太多了，还可以查看简单版的日志：

<img src="{{ site.url }}/images/2017/07/14_7.png" alt="14_7" />


回退到特定的版本:

<img src="{{ site.url }}/images/2017/07/14_8.png" alt="14_8" />


返回到刚才的版本：

<img src="{{ site.url }}/images/2017/07/14_9.png" alt="14_9" />


## 三：理解工作区与暂存区的区别？

工作区：就是你在电脑上看到的目录，比如目录下testgit里的文件(.git隐藏目录版本库除外)。或者以后需要再新建的目录文件等等都属于工作区范畴。

版本库(Repository)：工作区有一个隐藏目录.git,这个不属于工作区，这是版本库。其中版本库里面存了很多东西，其中最重要的就是stage(暂存区)，还有Git为我们自动创建了第一个分支master,以及指向master的一个指针HEAD。

我们前面说过使用Git提交文件到版本库有两步：

第一步：是使用 git add 把文件添加进去，实际上就是把文件添加到暂存区。

第二步：使用git commit提交更改，实际上就是把暂存区的所有内容提交到当前分支上。



## 四：Git撤销修改


<img src="{{ site.url }}/images/2017/07/14_10.png" alt="14_10" />

<img src="{{ site.url }}/images/2017/07/14_11.png" alt="14_11" />



## 五：分支

在  版本回填退里，你已经知道，每次提交，Git都把它们串成一条时间线，这条时间线就是一个分支。截止到目前，只有一条时间线，在Git里，这个分支叫主分支，即master分支。


<img src="{{ site.url }}/images/2017/07/14_13.png" alt="14_13" />


切换到dev分支

<img src="{{ site.url }}/images/2017/07/14_14.png" alt="14_14" />


创建testbranch分支，同时切换到testbranch分支上去。

<img src="{{ site.url }}/images/2017/07/14_15.png" alt="14_15" />

删除分支

<img src="{{ site.url }}/images/2017/07/14_19.png" alt="14_19" />

### 1.  下面举例说明如何使用 分支 来帮助开发。


首先进入master分支，查看readme.txt文件内容，只有aaa,bbb,ccc

<img src="{{ site.url }}/images/2017/07/14_16.png" alt="14_16" />

然后切换到dev分支，往readme.txt文件中添加 ddd，并提交修改。

<img src="{{ site.url }}/images/2017/07/14_17.png" alt="14_17" />

接着切换回master分支，查看reame.txt文件并没有ddd，然后我们执行merge动作，把dev分支merge到master分支上。

<img src="{{ site.url }}/images/2017/07/14_18.png" alt="14_18" />


**分支策略：**首先master主分支应该是非常稳定的，也就是用来发布新版本，一般情况下不允许在上面干活，干活一般情况下在新建的dev分支上干活，干完后，比如上要发布，或者说dev分支代码稳定后可以合并到主分支master上来。


### 2. 合并分支时，如何解决冲突？

先把刚才的分支，东西全部删了。

<img src="{{ site.url }}/images/2017/07/14_20.png" alt="14_20" />

然后创建dev分支，并向readme.txt添加eee，并提交修改。

<img src="{{ site.url }}/images/2017/07/14_21.png" alt="14_21" />

切换到master分支，并向readme.txt添加ffff，并提交修改。

<img src="{{ site.url }}/images/2017/07/14_22.png" alt="14_22" />

在master分支下执行merge操作，然后readme.txt文件会出现冲突，修改readme.txt文件，修改好了之后提交readme.txt。

<img src="{{ site.url }}/images/2017/07/14_23.png" alt="14_23" />

查看merge的日志。

<img src="{{ site.url }}/images/2017/07/14_28.png" alt="14_28" />



## 六：远程仓库。


那么如何添加远程库？

<img src="{{ site.url }}/images/2017/07/14_24.png" alt="14_24" />

<img src="{{ site.url }}/images/2017/07/14_25.png" alt="14_25" />

<img src="{{ site.url }}/images/2017/07/14_26.png" alt="14_26" />

<img src="{{ site.url }}/images/2017/07/14_27.png" alt="14_27" />

从现在起，只要本地作了提交，就可以通过如下命令：

git push origin master

把本地master分支的最新修改推送到github上了，现在你就拥有了真正的分布式版本库了。


## 七：.gitignore文件

这个文件的作用就是告诉Git哪些文件不需要添加到版本管理中。

在这个文件里面可以写上编译时生成的中间文件，那么在 git status, git add 的时候，就不会被提交了。


## Git基本常用命令如下：

   mkdir：         XX (创建一个空目录 XX指目录名)

   pwd：          显示当前目录的路径。

   git init          把当前的目录变成可以管理的git仓库，生成隐藏.git文件。

   git add XX       把xx文件添加到暂存区去。

   (use "git rm --cached <file>..." to unstage) 这个命令是用来取消暂存区中的文件的。

   git commit –m “XX”  提交文件 –m 后面的是注释。

   git status        查看仓库状态

   git diff  XX      查看XX文件修改了那些内容

   git log          查看历史记录

   git reset  –hard HEAD^ 或者 git reset  –hard HEAD~ 回退到上一个版本

                        (如果想回退到100个版本，使用git reset –hard HEAD~100 )

   cat XX         查看XX文件内容

   git reflog       查看历史记录的版本号id

   git checkout — XX  把XX文件在工作区的修改全部撤销。

   git rm XX          删除XX文件

   git remote add origin https://github.com/tugenhua0707/testgit 关联一个远程库

   git push –u(第一次要用-u 以后不需要) origin master 把当前master分支推送到远程库

   git clone https://github.com/tugenhua0707/testgit  从远程库中克隆

   git checkout –b dev  创建dev分支 并切换到dev分支上

   git branch  查看当前所有的分支

   git checkout master 切换回master分支

   git merge dev    在当前的分支上合并dev分支

   git branch –d dev 删除dev分支

   git branch name  创建分支

   git stash 把当前的工作隐藏起来 等以后恢复现场后继续工作

   git stash list 查看所有被隐藏的文件列表

   git stash apply 恢复被隐藏的文件，但是内容不删除

   git stash drop 删除文件

   git stash pop 恢复文件的同时 也删除文件

   git remote 查看远程库的信息

   git remote –v 查看远程库的详细信息

   git push origin master  Git会把master分支推送到远程库对应的远程分支上




## 参考链接

<a href="http://blog.jobbole.com/78960/" target="_blank">http://blog.jobbole.com/78960/</a>





