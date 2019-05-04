---
layout: post
title:  "最大公约数"
date:   2016-05-04
comments: true
categories: Art
tags: [Art,Algorithm,C]
description:
published: true
---

```c++
#include <stdio.h>

int gcd(int p, int q)
{
    if(q == 0) return p;
    int r = p % q;
    return gcd(q, r);
}

int main()
{
    printf("%d\n", gcd(30, 24));  //输出:6
    printf("%d\n", gcd(30, 7));  //输出:1
    printf("%d\n", gcd(28, 56));  //输出:28

    getchar();getchar();
    return 0;
}
```

**辗转相除法**

用（a,b）来表示a和b的最大公约数。

有定理： 已知a,b,c为正整数，若a除以b余c，则（a,b）=(b,c)。 （证明过程请参考其它资料）

```
例1： 30 和 7
∵ 30 % 7 = 2   ∴ (30,7) = (7,2)
∵ 7 % 2 = 1    ∴ (7,2) = (2,1)
2 % 1 = 0
所以 30 和 7 的最大公约数是 1
```

```
例1： 30 和 24
30 % 24 = 6
24 % 6 = 0
所以 30 和 24 的最大公约数是 6
```
