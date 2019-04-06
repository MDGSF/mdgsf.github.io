---
layout: post
title: "[Art][leetcode] 405 Convert a Number to Hexadecimal"
date: 2016-09-25
author: mdgsf
comments: true
categories: Art
tags: [Leetcode]
description:
published: true #default true
---

[https://leetcode.com/problems/convert-a-number-to-hexadecimal/description/](https://leetcode.com/problems/convert-a-number-to-hexadecimal/description/)

## 题目

 Given an integer, write an algorithm to convert it to hexadecimal. For negative integer, (two’s complement)[https://en.wikipedia.org/wiki/Two%27s_complement] method is used.

IMPORTANT:
You must not use any method provided by the library which converts/formats the number to hex directly. Such solution will result in disqualification of all your submissions to this problem. Users may report such solutions after the contest ends and we reserve the right of final decision and interpretation in the case of reported solutions.

**Note:**

1. All letters in hexadecimal (a-f) must be in lowercase.
2. The hexadecimal string must not contain extra leading 0s. If the number is zero, it is represented by a single zero character '0'; otherwise, the first character in the hexadecimal string will not be the zero character.
3. The given number is guaranteed to fit within the range of a 32-bit signed integer.
4. You **must not use any method provided by the library** which converts/formats the number to hex directly.

**Example 1:**

```
Input:
26

Output:
"1a"
```

**Example 2:**

```
Input:
-1

Output:
"ffffffff"
```

## 题目翻译

## 题目解析

## 参考答案

```cpp
class Solution {
public:
    string toHex(int num) {
        if(num == 0)
        {
            return "0";
        }
        
        unsigned int uiNum = (unsigned int)num;
        
        string s2;
        unsigned int uiLeft;
        while (uiNum != 0)
        {
            uiLeft = uiNum % 16;
            if(uiLeft <= 9)
            {
                s2 += uiLeft + '0';
            }
            else
            {
                s2 += uiLeft + 'a' - 10;
            }
            uiNum /= 16;
        }
        
        char c;
        for (int i = 0, j = s2.size() - 1; i < j; i++, j--)
        {
            c = s2[i];
            s2[i] = s2[j];
            s2[j] = c;
        }
        
        return s2;
    }
};
```
