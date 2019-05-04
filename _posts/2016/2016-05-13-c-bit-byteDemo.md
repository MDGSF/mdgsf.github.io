---
layout: post
title:  "[C/C++] 位操作 例子"
date:   2016-05-13
comments: true
categories: C/C++
tags: [C,Bit]
description:
published: true
---

```cpp
#include <stdio.h>

#define B2IL(b) (((b)[0] & 0xFF) | (((b)[1] << 8) & 0xFF00) | (((b)[2] << 16) & 0xFF0000) | (((b)[3] << 24) & 0xFF000000))
#define B2IU(b) (((b)[3] & 0xFF) | (((b)[2] << 8) & 0xFF00) | (((b)[1] << 16) & 0xFF0000) | (((b)[0] << 24) & 0xFF000000))

int main()
{
    char acWord[1024] = {0};
    acWord[0] = 'a';
    acWord[1] = 'b';
    acWord[2] = 'c';
    acWord[3] = 'd';
    printf("acWord: %s\n", acWord);
    printf("acWord: %c %c %c %c\n", acWord[0], acWord[1], acWord[2], acWord[3]);
    printf("acWord: %d %d %d %d\n", acWord[0], acWord[1], acWord[2], acWord[3]);
    printf("acWord: %x %x %x %x\n", acWord[0], acWord[1], acWord[2], acWord[3]);
    printf("\n");


    char *pcWord = acWord;
    printf("pcWord: %s\n", pcWord);
    printf("pcWord: %c %c %c %c\n", pcWord[0], pcWord[1], pcWord[2], pcWord[3]);
    printf("pcWord: %d %d %d %d\n", pcWord[0], pcWord[1], pcWord[2], pcWord[3]);
    printf("pcWord: %x %x %x %x\n", pcWord[0], pcWord[1], pcWord[2], pcWord[3]);
    printf("#define B2IL(b) (((b)[0] & 0xFF) | (((b)[1] << 8) & 0xFF00) | (((b)[2] << 16) & 0xFF0000) | (((b)[3] << 24) & 0xFF000000))\n");
    printf("#define B2IU(b) (((b)[3] & 0xFF) | (((b)[2] << 8) & 0xFF00) | (((b)[1] << 16) & 0xFF0000) | (((b)[0] << 24) & 0xFF000000))\n");
    printf("B2IL(pcWord): %x\n", B2IL(pcWord));
    printf("B2IU(pcWord): %x\n", B2IU(pcWord));

    return 0;
}
```
