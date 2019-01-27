---
layout: post
title:  "[C/C++] 并查集 Disjoint set"
date:   2016-12-27
comments: true
categories: C/C++
tags: [C]
description: ""
published: true
---



```cpp
#include <stdio.h>

#define DISJOINT_SIZE 10

int iGetRoot(int aiDisjointSet[], int i)
{
	int iCur = i;
	while (aiDisjointSet[iCur] != iCur)
	{
		iCur = aiDisjointSet[iCur];
	}
	return iCur;
}

void vUnion(int aiDisjointSet[], int x, int y)
{
	int iXRoot = iGetRoot(aiDisjointSet, x);
	int iYRoot = iGetRoot(aiDisjointSet, y);
	aiDisjointSet[iYRoot] = iXRoot;
}

int iFind(int aiDisjointSet[], int i)
{
	int iCur = i;
	int iRoot = iGetRoot(aiDisjointSet, iCur);
	
	while (aiDisjointSet[iCur] != iRoot)
	{
		int t = aiDisjointSet[iCur];
		aiDisjointSet[iCur] = iRoot;
		iCur = t;
	}
	return iRoot;
}

void vShow(int aiDisjointSet[], int iSetSize)
{
	for (int i = 0; i < iSetSize; i++)
	{
		printf("%d ", aiDisjointSet[i]);
	}
	printf("\n");
}

int main()
{
	int i;

	//initialize disjoint set
	int aiDisjointSet[DISJOINT_SIZE] = {0};
	for (i = 0; i < DISJOINT_SIZE; i++)
	{
		aiDisjointSet[i] = i;
	}
	vShow(aiDisjointSet, DISJOINT_SIZE);


	vUnion(aiDisjointSet, 1, 2);
	vUnion(aiDisjointSet, 3, 4);
	vUnion(aiDisjointSet, 2, 4);
	vShow(aiDisjointSet, DISJOINT_SIZE);


	printf("%d\n", iFind(aiDisjointSet, 4));
	vShow(aiDisjointSet, DISJOINT_SIZE);

	return 0;
}
```





