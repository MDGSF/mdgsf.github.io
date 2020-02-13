---
layout: post
title:  "[算法学习][LintCode] A + B"
date:   2016-06-15
comments: true
categories: Art
tags: [C,A+b]
description:
published: true
---

要求：不用加减乘除实现A+B。

```cpp
int aplusb(int a, int b) {
    while(b != 0){
        int carry = a & b;
        a = a ^ b;
        b = carry << 1;
    }
    return a;
}
```

x^y //执行加法，不考虑进位。

(x&y)<<1 //进位操作
