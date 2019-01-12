---
layout: post
title:  "[C] strstr 不使用KMP达到O(n)的时间复杂度"
date:   2016-12-28
comments: true
categories: c
tags: c 
description: ""
published: true
---

### 问题

```
哈希表应用：字符串匹配
设字符串A="12314123"
求 "123" 在A中出现的次数
不会写KMP又要O(n)的时间复杂度。

Key( "123" ) = '1' * 10^2 + '2' * 10 + '3' = 123

HashMap = [123, 231, 314, 141, 412, 123]

但是这个时候为了求每个自串，时间复杂度还是O(n*m)
所以，这时候需要用到一个小技巧。

对字符串 "1234"
1 * 10^2 + 2 * 10 + 3  (式子A)
2 * 10^2 + 3 * 10 + 4  (式子B)
A的后半部分和B的前半部分很像，去掉冗余，
(A - 1*10^2) * 10 + 4 = B
这样计算子串就是O(1)的时间，总的时间复杂度就是O(n)


其实关键在于哈希函数，本来每个子串都要计算一次Key，现在利用了前一个子串的Key，降低了时间复杂度。
```


### 代码

```cpp
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef struct _SNode {
	struct _SNode * next;
	char m_acSubStr[BUFSIZ];
	int m_iNum;

	_SNode() {
		memset(m_acSubStr, 0, sizeof(m_acSubStr));
		m_iNum = 0;
		next = NULL;
	}

	_SNode(const char * pcStr) {
		strncpy(m_acSubStr, pcStr, sizeof(m_acSubStr));
		m_iNum = 1;
		next = NULL;
	}
}SNode;

typedef struct _SHashMap {
	int m_iHashMapCapacity;
	SNode * m_piHashMap;

	_SHashMap(int iHashMapCapacity, SNode * piHashMap)
	{
		m_iHashMapCapacity = iHashMapCapacity;
		m_piHashMap = piHashMap;
	}
}SHashMap;

SHashMap * hm_Init(int iHashMapCapacity)
{
	SNode * piHashMap = (SNode*)malloc(sizeof(SNode) * iHashMapCapacity);
	SHashMap * pstHashMap = new SHashMap(iHashMapCapacity, piHashMap);
	return pstHashMap;
}

void hm_Insert(SHashMap * pstHashMap, int iKey, const char * pcStr)
{
	//printf("hm_Insert() iKey = %d, pcStr = %s\n", iKey, pcStr);

	SNode * pHead = &(pstHashMap->m_piHashMap[iKey]);

	bool bFound = false;
	SNode * pCur = pHead;
	while (pCur->next != NULL)
	{
		pCur = pCur->next;
		if(strcmp(pCur->m_acSubStr, pcStr) == 0)
		{
			pCur->m_iNum++;
			bFound = true;
		}
	}

	if(!bFound)
	{
		SNode * pstNewNode = new SNode(pcStr);
		pCur->next = pstNewNode;
	}
}

SNode * hm_find(SHashMap * pstHashMap, int iKey, const char * pcStr)
{
	SNode * pHead = &(pstHashMap->m_piHashMap[iKey]);

	SNode * pCur = pHead;
	while (pCur->next != NULL)
	{
		pCur = pCur->next;
		if(strcmp(pCur->m_acSubStr, pcStr) == 0)
		{
			return pCur;
		}
	}

	return NULL;
}

int iHashFunction(const char * pcStr, int iHashMapCapacity)
{
	int iKey = 0;
	int iStrLen = strlen(pcStr);
	for (int i = 0; i < iStrLen; i++) {
		iKey = (iKey * 10 + pcStr[i]) % iHashMapCapacity;
	}
	return iKey;
}

int HashStrstr(const char * str1, const char * str2)
{
	if(NULL == str1 || NULL == str2) {
		return 0;
	}

	int i = 0;
	int j = 0;
	int iKey = 0;
	int iPreKey = -1;
	int iRatio = 0;

	int iLen1 = strlen(str1);
	int iLen2 = strlen(str2);
	if(iLen1 <= 0 || iLen2 <= 0 || iLen1 < iLen2) {
		return 0;
	}
	//printf("[%s:%d], [%s:%d]\n", str1, iLen1, str2, iLen2);

	iRatio = 1;
	for (i = 0; i < iLen2 - 1; i++) {
		iRatio *= 10;
	}

	int iSubStrNum = iLen1 - iLen2 + 1;
	int iHashMapCapacity = iSubStrNum * 3;
	SHashMap * pstHashMap = hm_Init(iHashMapCapacity);
	//printf("iSubStrNum = %d, iHashMapCapacity = %d\n", iSubStrNum, iHashMapCapacity);

	for (i = 0; i < iSubStrNum; i++)
	{
		char acSubStr[BUFSIZ] = {0};
		for (j = 0; j < iLen2; j++) {
			acSubStr[j] = str1[i+j];
		}

		if(iPreKey == -1)
		{
			iKey = iHashFunction(acSubStr, iHashMapCapacity);
		}
		else
		{
			iKey = ((iPreKey - (str1[i-1]*iRatio)%iHashMapCapacity + iHashMapCapacity)*10 + acSubStr[iLen2-1])%iHashMapCapacity;
		}
		iPreKey = iKey;

		hm_Insert(pstHashMap, iKey, acSubStr);
	}
	
	iKey = iHashFunction(str2, iHashMapCapacity);
	SNode * pNode = hm_find(pstHashMap, iKey, str2);
	return pNode == NULL ? 0 : pNode->m_iNum;
}


void vTest(const char * pcCaseName, const char * pcStr1, const char * pcStr2, int iExpectResult)
{
	int iRet = HashStrstr(pcStr1, pcStr2);
	if(iRet == iExpectResult)
	{
		printf("%s: pass\n", pcCaseName);
	}
	else
	{
		printf("%s: failed, iRet = %d\n", pcCaseName, iRet);
	}
}

void vTest1()
{
	vTest("vTest1", "1234", "123", 1);
}

void vTest2()
{
	vTest("vTest2", "1234123", "123", 2);
}

void vTest3()
{
	vTest("vTest3", "1231231231123", "123", 4);
}

void vTest4()
{
	vTest("vTest4", "1231231231123", "1", 5);
}

void vTest5()
{
	vTest("vTest5", "abcd", "1", 0);
}

int main()
{
	vTest1();
	vTest2();
	vTest3();
	vTest4();
	vTest5();
	return 0;
}
```
















