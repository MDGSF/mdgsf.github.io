---
layout: post
title:  "[数学] prime generate"
date:   2016-03-11
comments: true
categories: Art
tags: art, algorithm, c, prime
description:
published: true
---

### 生成10000以内的所有素数

```cpp
#include <stdio.h>

#define N 10000

int main()
{
    int i;
    int j;
    int a[N];

    for (i = 2; i < N; i++) a[i] = 1;

    for (i = 2; i < N; i++)
    {
        if(a[i])
        {
            for (j = i; i*j < N; j++)
                a[i*j] = 0;
        }
    }

    for (i = 2; i < N; i++)
        if(a[i])
            printf("%4d ", i);
    printf("\n");
    return 0;
}
```


