---
layout: post
title: "[Network] 协程"
date: 2020-06-10
author: mdgsf
comments: true
categories: Network
tags: [Network]
description:
published: true #default true
---

### ucontext

ucontext 提供了 4 个函数，ubuntu 系统上头文件在 `/usr/include/ucontext.h`。

```cpp
/* Get user context and store it in variable pointed to by UCP.  */
extern int getcontext (ucontext_t *__ucp) __THROWNL;

/* Set user context from information of variable pointed to by UCP.  */
extern int setcontext (const ucontext_t *__ucp) __THROWNL;

/* Save current context in context variable pointed to by OUCP and set
   context from variable pointed to by UCP.  */
extern int swapcontext (ucontext_t *__restrict __oucp,
			const ucontext_t *__restrict __ucp) __THROWNL;

/* Manipulate user context UCP to continue with calling functions FUNC
   and the ARGC-1 parameters following ARGC when the context is used
   the next time in `setcontext' or `swapcontext'.

   We cannot say anything about the parameters FUNC takes; `void'
   is as good as any other choice.  */
extern void makecontext (ucontext_t *__ucp, void (*__func) (void),
			 int __argc, ...) __THROW;
```

可以使用 `man getcontext`、`man setcontext`、`man swapcontext`、`man makecontext` 来
查看详细帮助文档。

### 相关资源

- [`MDGSF/coroutine`]
- [`一个“蝇量级” C 语言协程库`]
- [`Cyber RT 协程技术解读`]
- [`cyber/croutine`]
- [`Goroutine`]
- [`Tencent/libco`]
- [`cloudwu/coroutine`]
- [`Portable Multithreading`]
- [`Winnerhust/uthread`]
- [`人人都可以实现的简单协程库`]
- [`实现c协程`]

[`MDGSF/coroutine`]: https://github.com/MDGSF/coroutine
[`一个“蝇量级” C 语言协程库`]: https://coolshell.cn/articles/10975.html
[`Cyber RT 协程技术解读`]: https://mp.weixin.qq.com/s?__biz=MzI1NjkxOTMyNQ==&mid=2247486780&idx=1&sn=6e406ca08ed9eae02040cf6f4db8e784&scene=21#wechat_redirect
[`cyber/croutine`]: https://github.com/ApolloAuto/apollo/tree/master/cyber/croutine
[`Goroutine`]: https://github.com/golang/go
[`Tencent/libco`]: https://github.com/Tencent/libco
[`cloudwu/coroutine`]: https://github.com/cloudwu/coroutine
[`Portable Multithreading`]: http://xmailserver.org/rse-pmt.pdf
[`Winnerhust/uthread`]: https://github.com/Winnerhust/uthread
[`人人都可以实现的简单协程库`]: https://blog.csdn.net/qq910894904/article/details/41911175
[`实现c协程`]: https://www.cnblogs.com/sniperHW/archive/2012/06/19/2554574.html
