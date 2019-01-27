---
layout: post
title: "[Art][leetcode - C++] 20 Valid Parentheses"
date: 2016-08-21
author: mdgsf
comments: true
categories: Art
tags: [Leetcode]
description:
published: true #default true
---

[https://leetcode.com/problems/valid-parentheses/description/](https://leetcode.com/problems/valid-parentheses/description/)

## 题目

Given a string containing just the characters '(', ')', '{', '}', '[' and ']', determine if the input string is valid.

The brackets must close in the correct order, "()" and "()[]{}" are all valid but "(]" and "([)]" are not.

## 题目翻译

## 题目解析

## 参考答案

```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef struct _SStack {
    char * pcHead;
    int iSize;
    int iMaxSize;
}SStack;

SStack * stack_init(int iLen) {
    SStack * pStack = (SStack*)malloc(sizeof(SStack));
    pStack->pcHead = (char*)malloc(sizeof(iLen));
    pStack->iSize = 0;
    pStack->iMaxSize = iLen;
    return pStack;
}

void stack_push(SStack * pStack, char c) {
    if(pStack->iSize >= pStack->iMaxSize) {
        return ;
    }

    pStack->pcHead[ pStack->iSize ] = c;
    pStack->iSize++;
}

char stack_pop(SStack * pStack) {
    if(pStack->iSize <= 0) {
        return -1;
    }

    pStack->iSize--;
    return pStack->pcHead[ pStack->iSize ];
}

bool stack_isEmpty(SStack * pStack) {
    return pStack->iSize == 0;
}

bool isValid(char * s) {
    int iLen = strlen(s);
    
    SStack * pStack = stack_init(iLen);
    for (int i = 0; i < iLen; i++)
    {
        char c = s[i];
        if(c == ')') {
            if(stack_isEmpty(pStack)) {
                return false;
            }
            char top = stack_pop(pStack);
            if(top != '(') {
                return false;
            }
        } else if(c == ']') {
            char top = stack_pop(pStack);
            if(top != '[') {
                return false;
            }
        } else if(c == '}') {
            char top = stack_pop(pStack);
            if(top != '{') {
                return false;
            }
        } else {
            stack_push(pStack, c);
        }
    }

    return stack_isEmpty(pStack);
}

int main()
{
    printf("%d\n", isValid("[{}]()[[[[(({}))]]]]"));
    getchar();getchar();
    return 0;
}
```
