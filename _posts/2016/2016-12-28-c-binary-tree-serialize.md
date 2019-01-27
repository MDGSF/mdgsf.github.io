---
layout: post
title:  "[C/C++] 二叉树的序列化和反序列化"
date:   2016-12-28
comments: true
categories: C/C++
tags: [C]
description: ""
published: true
---


```cpp
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <iostream>
#include <list>
using namespace std;

#define BT_SEP ','
#define EMPTY_NODE "#"

typedef struct _STreeNode {
	int m_val;
	struct _STreeNode * left;
	struct _STreeNode * right;
	_STreeNode(int val)
	{
		m_val = val;
		left = NULL;
		right = NULL;
	}
}SNode;

char * _bt_Trim(char * pcStr)
{
	if(NULL != pcStr)
	{
		for ( ; *pcStr && ::isspace(*pcStr); ++pcStr) ;

		char * pcLast = pcStr + strlen(pcStr) - 1;
		for ( ; pcLast >= pcStr && ::isspace(*pcLast); --pcLast)
		{
			*pcLast = 0;
		}
	}
	return pcStr;
}

SNode * bt_Init(const char * pcSerialize)
{
	printf("bt_Init()\n");
	if(NULL == pcSerialize || strlen(pcSerialize) <= 0) {
		return NULL;
	}

	SNode * root = NULL;
	list<SNode**> NodeList;
	NodeList.push_back(&root);

	int iSerializeLen = strlen(pcSerialize);
	char * pc = (char*)malloc(sizeof(char) * (iSerializeLen+1));
	strncpy(pc, pcSerialize, iSerializeLen);

	char * pcStart = _bt_Trim(pc);
	char * pcEnd = strchr(pcStart, BT_SEP);
	while (1)
	{
		if(pcEnd != NULL) {
			*pcEnd = '\0';
		}
		char acNode[BUFSIZ] = {0};
		strcpy(acNode, _bt_Trim(pcStart)); 

		if(strlen(acNode) <= 0)
		{
			NodeList.pop_front();
		}
		else if(strcmp(acNode, EMPTY_NODE) == 0)
		{
			if(root == NULL)
			{
				return NULL;
			}
			else
			{
				NodeList.pop_front();
			}
		}
		else
		{
			SNode * pNode = new SNode(atoi(acNode));
			SNode ** ppParent = NodeList.front();
			NodeList.pop_front();
			NodeList.push_back(&pNode->left);
			NodeList.push_back(&pNode->right);

			*ppParent = pNode;
		}

		if(NULL == pcEnd)
		{
			break;
		}
		
		pcStart = pcEnd + 1;
		pcEnd = strchr(pcStart, BT_SEP);
	}

	return root;
}

void _bt_Show(list<SNode*> & NodeList)
{
	SNode * pNode = NodeList.front();
	if(pNode != NULL)
	{
		printf("%d,", pNode->m_val);
		NodeList.push_back(pNode->left);
		NodeList.push_back(pNode->right);
		if(pNode->left != NULL) {
		}
		if(pNode->right != NULL) {
		}
	}
	else
	{
		if(!NodeList.empty())
		{
			printf("#,");
		}
	}
	NodeList.pop_front();

	if(!NodeList.empty())
	{
		_bt_Show(NodeList);
	}
}

void bt_Show(SNode * root)
{
	printf("bt_Show()\n");
	list<SNode*> NodeList;
	NodeList.push_back(root);
	_bt_Show(NodeList);
	printf("\n");
}

int main()
{
	printf("Hello World!\n");
	char ac[BUFSIZ] = {0};
	strcpy(ac, "1,2,3,4,#,6,7,#,9,");
	SNode * root = bt_Init(ac);
	if(NULL == root) {
		printf("bt_Init failed\n");
		return -1;
	}

	bt_Show(root);
	return 0;
}
```


