---
layout: post
title:  "[C/C++] 队列O(1)求最大元素"
date:   2016-12-26
comments: true
categories: C/C++
tags: [C]
description: ""
published: true
---



### CMaxStack.h

```cpp
#ifndef CMAX_STACK_HJ
#define CMAX_STACK_HJ

#include <stdio.h>
#include <stdlib.h>
#include <iostream>
#include <stack>
using namespace std;

class CMaxStack
{
public:
	CMaxStack();
	~CMaxStack();

	void m_vPush(int i);
	void m_vPop();
	int m_iMaxElement();
	bool m_bEmpty();
	int m_iTop();

private:
	stack<int> m_oStack;
	stack<int> m_MaxValueStack;
};


#endif
```


### CMaxStack.cpp

```cpp
#include "CMaxStack.h"

CMaxStack::CMaxStack()
{
}

CMaxStack::~CMaxStack()
{
}

void 
CMaxStack::m_vPush(int i)
{
	m_oStack.push(i);

	if(m_MaxValueStack.empty() || i >= m_MaxValueStack.top())
	{
		m_MaxValueStack.push(i);
	}
}

void
CMaxStack::m_vPop()
{
	if(m_MaxValueStack.top() == m_oStack.top())
	{
		m_MaxValueStack.pop();
	}

	m_oStack.pop();
}

int 
CMaxStack::m_iMaxElement()
{
	return m_MaxValueStack.top();
}

bool CMaxStack::m_bEmpty()
{
	return m_oStack.empty() ? true : false;
}

int CMaxStack::m_iTop()
{
	return m_oStack.top();
}
```


### CMaxQueue.h

```cpp
#ifndef CMAX_QUEUE_HJ
#define CMAX_QUEUE_HJ

#include "CMaxStack.h"

class CMaxQueue
{
public:
	CMaxQueue();
	~CMaxQueue();

	void m_vPush(int i);
	void m_vPop();
	int m_iMaxElement();
	bool m_bEmpty();

private:
	CMaxStack m_oPushStack;
	CMaxStack m_oPopStack;
};

#endif
```


### CMaxQueue.cpp

```cpp
#include "CMaxQueue.h"

CMaxQueue::CMaxQueue()
{
}

CMaxQueue::~CMaxQueue()
{
}

void 
CMaxQueue::m_vPush(int i)
{
	m_oPushStack.m_vPush( i );
}

void
CMaxQueue::m_vPop()
{
	if( m_oPopStack.m_bEmpty() )
	{
		while ( ! m_oPushStack.m_bEmpty() )
		{
			m_oPopStack.m_vPush( m_oPushStack.m_iTop() );
			m_oPushStack.m_vPop();
		}
		if( m_oPopStack.m_bEmpty() )
		{
			return ;
		}
	}

	m_oPopStack.m_vPop();
}

int 
CMaxQueue::m_iMaxElement()
{
	if(!m_oPushStack.m_bEmpty() && !m_oPopStack.m_bEmpty())
	{
		int iPushMax = m_oPushStack.m_iMaxElement();
		int iPopMax = m_oPopStack.m_iMaxElement();
		return iPushMax > iPopMax ? iPushMax : iPopMax;
	}
	else if(m_oPushStack.m_bEmpty() && !m_oPopStack.m_bEmpty())
	{
		return m_oPopStack.m_iMaxElement();
	}
	else if(!m_oPushStack.m_bEmpty() && m_oPopStack.m_bEmpty())
	{
		return m_oPushStack.m_iMaxElement();
	}
}

bool 
CMaxQueue::m_bEmpty()
{
	return (m_oPushStack.m_bEmpty() && m_oPopStack.m_bEmpty()) ? true : false;
}

```


### main.cpp

```cpp
#include "CMaxQueue.h"

int main()
{
	printf("Hello World!\n");

	CMaxQueue oQueue;

	int i;
	for (i = 0; i < 10; i++)
	{
		oQueue.m_vPush( i );
	}
	for (i = 10; i > 0; i--)
	{
		oQueue.m_vPush( i );
	}

	if(!oQueue.m_bEmpty()) {
		printf("oQueue.m_iMaxElement() = %d\n", oQueue.m_iMaxElement());
	}

	for (i = 0; i < 11; i++)
	{
		oQueue.m_vPop();
	}

	if(!oQueue.m_bEmpty()) {
		printf("oQueue.m_iMaxElement() = %d\n", oQueue.m_iMaxElement());
	}

	return 0;
}
```



