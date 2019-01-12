---
layout: post
title:  "[C] Word Count"
date:   2016-09-22
comments: true
categories: c
tags: c
description:
published: true
---


### 计算每个字符出现的次数

```cpp
#include <stdio.h>

int main()
{
	int i;
	int c;
	unsigned char ucAscii[256] = {0};

	while ( (c = getchar()) != EOF )
	{
		if(c >= 0 && c < 256)
		{
			ucAscii[c]++;
		}
	}

	for (i = 0; i < 256; i++)
	{
		printf("(%d|%c):[%d]\n", i, (unsigned char)i, ucAscii[i]);
	}
	return 0;
}
```





### 计算每个单词出现的次数


```cpp
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define MAX_WORD_NUM 10000

typedef struct _SWord
{
	char m_acWord[BUFSIZ];
	int m_iCount;
}SWord;

SWord g_acWord[MAX_WORD_NUM] = {0};
int g_iWordIndex = 0;

int main()
{
	int i;
	int bExists;
	char acWord[BUFSIZ] = {0};
	while (scanf("%s", acWord) != EOF)
	{
		bExists = 0;
		for (i = 0; i < g_iWordIndex; i++)
		{
			if(strcmp(g_acWord[i].m_acWord, acWord) == 0)
			{
				bExists = 1;
				g_acWord[i].m_iCount++;
				break;
			}
		}

		if(!bExists)
		{
			strncpy(g_acWord[g_iWordIndex].m_acWord, acWord, BUFSIZ);
			g_acWord[g_iWordIndex].m_iCount = 1;
			g_iWordIndex++;
		}
	}

	for (i = 0; i < g_iWordIndex; i++)
	{
		printf("%s:[%d]\n", g_acWord[i].m_acWord, g_acWord[i].m_iCount);
	}
	return 0;
}

```

这是用数组来保存的，需要遍历整个数组，效率不高。

换成 **hash表** 或者 **红黑树** 可以提高效率。

最后给一个C++的实现。


```cpp
#include <iostream>
#include <string>
#include <map>
#include <stdio.h>

using namespace std;

int main()
{
	char acWord[BUFSIZ] = {0};
	map<string, int> wordmap;
	while (scanf("%s", acWord) != EOF)
	{
		string strWord = acWord;
		map<string, int>::iterator iter = wordmap.find(strWord);
		if(iter == wordmap.end())
		{
			wordmap.insert( std::pair<string, int>(strWord, 1) );
		}
		else
		{
			iter->second++;
		}
	}


	map<string, int>::iterator iter = wordmap.begin();
	while (iter != wordmap.end())
	{
		printf("%s[%d]\n", const_cast<char*>(iter->first.c_str()), iter->second);
		++iter;
	}

	return 0;
}
```









