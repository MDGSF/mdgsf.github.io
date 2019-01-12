---
layout: post
title: "[Art] [leetcode - C++] 3 Longest Substring without Repeating Characters"
date: 2017-07-20
author: mdgsf
comments: true
categories: Art
tags: leetcode
description:
published: true #default true
---

[https://leetcode.com/problems/longest-substring-without-repeating-characters/description/](https://leetcode.com/problems/longest-substring-without-repeating-characters/description/)

## 题目

Given a string, find the length of the longest substring without repeating characters.

Examples:

Given "abcabcbb", the answer is "abc", which the length is 3.

Given "bbbbb", the answer is "b", with the length of 1.

Given "pwwkew", the answer is "wke", with the length of 3. Note that the answer must be a substring, "pwke" is a subsequence and not a substring.

## 题目翻译

## 题目解析

## 参考答案

```cpp
#include <iostream>
#include <map>
#include <set>
#include <stdio.h>
using namespace std;


class Solution {
public:
    int lengthOfLongestSubstring(string s) {

        int iLen = s.length();

        int iMaxSubLen = 0;
        int iSubLen = 0;
        int iSubStart = 0;
        int iSubEnd = 0;
        set<char> setCurSubCharacter;
        set<char>::iterator iter;

        for (int i = 0; i < iLen; i++)
        {
            char c = s[i];
            if(setCurSubCharacter.size() == 0)
            {
                setCurSubCharacter.insert(c); 
                iSubStart = i;
                iSubEnd = i;
                iSubLen = 1;
                if(iSubLen > iMaxSubLen)
                {
                    iMaxSubLen = iSubLen;
                }
            }
            else
            {
                iter = setCurSubCharacter.find(c);
                if(iter != setCurSubCharacter.end())
                {
                    iSubEnd++;
                    while(1)
                    {
                        if(s[iSubStart] == c)
                        {
                            ++iSubStart;
                            break;
                        }
                        else
                        {
                            setCurSubCharacter.erase(s[iSubStart]);
                            ++iSubStart;
                        }
                    }
                    iSubLen = iSubEnd - iSubStart + 1;
                    if(iSubLen > iMaxSubLen)
                    {
                        iMaxSubLen = iSubLen;
                    }
                }
                else
                {
                    setCurSubCharacter.insert(c);
                    iSubEnd++;
                    iSubLen++;
                    if(iSubLen > iMaxSubLen)
                    {
                        iMaxSubLen = iSubLen;
                    }
                }
            }
        }

        return iMaxSubLen;
    }
};


int main()
{
    string s = "dvdf";
    Solution o;
    int iRet = o.lengthOfLongestSubstring(s);
    return 0;
}
```