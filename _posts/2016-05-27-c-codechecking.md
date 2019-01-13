---
layout: post
title:  "[C/C++] 代码检查"
date:   2016-05-27
comments: true
categories: C/C++
tags: c, codechecking
description:
published: true
---

### 写完代码检查流程

```
1. 检查每个变量名是否合法，是否合理。

2. 检查每个函数名是否合法，是否合理。(函数是一个操作，通常是 动词+名词)

3. 检查类名是否合法，是否合理。(类名通常不应该出现动词)

4. malloc 是否有对应的 free。

5. new 是否有对应的 delete。

6. apr_thread_mutex_lock() 对应的 apr_thread_mutex_unlock() 不要写错了。

7. 放在 for，while 循环中的临时变量申明 是否可以放到循环的外面。

8. 循环体中的代码过长，考虑是否封装成一个函数替代。

9. 失败的时候记得输出log，成功的关键路径上也要记得输入log。

10. 对函数的输入参数进行合法性检查。

11. 将类的成员变量初始化。

12. 实现完一个函数，记得考虑是否需要多线程安全。

13. 对指针操作前，先判断是否为NULL。

14. 一个变量如果可以作为局部变量，就没有必要让它成为成员变量。

15. 给类加上

private:
    CClass(const CClass&);
    CClass& operator = (const CClass&);


16. 检查 if 和 while 中的 == 有没有写错了，写成了一个 = 。


```
