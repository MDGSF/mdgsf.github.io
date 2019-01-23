---
layout: post
title: "[字符串] Trim blank"
date: 2016-12-06
author: mdgsf
comments: true
categories: Art
tags: [C]
description:
published: true #default true
---

去除字符串的开头和结尾的空格，内部若有连续空格之保留一个。


```cpp
#include <stdio.h>
#include <string.h>

void TrimBlank(char * s)
{
	if(NULL == s)
	{
		return ;
	}

	int iLen = strlen(s);
	if(iLen <= 0)
	{
		return ;
	}

	//trim left
	int i = 0;
	for (i = 0; i < iLen; i++)
	{
		if(s[i] != ' ')
		{
			break;
		}
	}
	if(i == iLen) //this means string is all blank
	{
		return ;
	}


	//trim right
	char * pcRight = &s[iLen - 1];
	while (*pcRight == ' ')
	{
		pcRight--;
	}
	*(pcRight+1) = '\0';


	//trim middle duplicate blank
	bool bInSpace = false;
	int iNewIndex = 0;
	char *pcStart = &s[i];
	iLen = strlen(pcStart);
	for (i = 0; i < iLen; i++)
	{
		if(pcStart[i] == ' ')
		{
			if(bInSpace)
			{
				continue;
			}
			else
			{
				s[iNewIndex++] = pcStart[i];
			}
			bInSpace = true;
		}
		else
		{
			s[iNewIndex++] = pcStart[i];
			bInSpace = false;
		}
	}
	s[iNewIndex] = '\0';
}

int main()
{
	char ac[] = "  This is   a happy   day    , only    ll ";
	TrimBlank(ac);
	printf("%s\n", ac);
	return 0;
}
```


