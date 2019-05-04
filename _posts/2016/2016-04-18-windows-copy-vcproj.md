---
layout: post
title:  "[Windows] vs2008 两个工程文件用同一份代码"
date:   2016-04-18
comments: true
categories: Windows
tags: [Windows]
description:
published: true
---

在vs2008下，工程的名字叫 pro1.vcproj

将pro1.vcproj 复制为 pro2.vcproj

这个时候，pro1.vcproj 和 pro2.vcproj 就是两个不同的工程，但是共用着同一份代码。

用vs2008打开 pro2.vcproj ，build，有可能会出现下面的错误。按照方法修改了就可以了。

**error C2471：** cannot update program database vc90.pdb

这个vs2008一个著名的bug。详情可以参见[https://connect.microsoft.com/VisualStudio/feedback/ViewFeedback.aspx?FeedbackID=309462](https://connect.microsoft.com/VisualStudio/feedback/ViewFeedback.aspx?FeedbackID=309462)

官方现有解决方案如下：

I have found an alternate way for the time beging to avoid C2471 error but it works only in the case of successful release build.
for this click Build menu than Configuration manager than create a new setting from release settings. Change following things in your project settings as :

```
C\C++ | General | Debug Information format | C7 Compatible (/Z7)

C\C++ | Code Generation | Enable String Pooling | Yes (/GF)

Linker |Debuging |General Debug Info | Yes (/DEBUG)
```

**问题:**

那么如何在同一份代码里面区分是 pro1.vcproj 还是 pro2.vcproj 在build ？

用宏定义:

```
#ifdef PRO1
...  //在这里做只有 pro1 需要做的事
#endif

#ifdef PRO2
...  //在这里做只有 pro2 需要做的事
#endif
```

**问题:**

那 PRO1 和 PRO2 这两个宏要从哪里来获取？

在工程目录下建立子目录 type\pro1 和 子目录 type\pro2 ，

在这两个目录下都建立一个 type.h 的文件

```
//type\pro1\type.h
#ifndef PRO1
#define PRO1
#endif
```

```
//type\pro2\type.h
#ifndef PRO2
#define PRO2
#endif
```

然后在 pro1.vcproj 中将 type\pro1\type.h 包含进去。

在 pro2.vcproj 中将 type\pro2\type.h 包含进去。

怎么包含进去？

在工程的属性--> C/C++ --> Additional Include Directories
