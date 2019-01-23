---
layout: post
title:  "[基础] 判断是否是闰年"
date:   2016-03-30
comments: true
categories: Art
tags: [Art,Algorithm,C]
description: "Leap year"
published: true
---

正常一年有365天，2月28天。

闰年有366天，2月29天。

```cpp
int iDaysPerYear=365;
if(((iYear%4==0) && (iYear%100!=0)) || (iYear%400==0))
{
    iDaysPerYear+=1;
}
return iDaysPerYear;
```
