---
layout: post
title:  "[C/C++] virtual"
date:   2017-03-02
comments: true
categories: C/C++
tags: [C++,Virtual]
description:
published: true
---


```cpp
#include <stdio.h>

class CPolygon 
{
public:
	void set_values(int a, int b)
	{
		width = a;
		height = b;
	}

	virtual int area()
	{
		return 0;
	}

	void name()
	{
		printf("CPolygon\n");
	}

protected:
	int width;
	int height;
};

class CRectangle : public CPolygon
{
public:
	int area() 
	{
		return width * height;
	}

	void name()
	{
		printf("CRectangle\n");
	}
};

class CTriangle : public CPolygon
{
public:
	int area() 
	{
		return width * height / 2;
	}

	void name()
	{
		printf("CTriangle\n");
	}
};

int main()
{
	CPolygon poly;
	CRectangle rect;
	CTriangle trgl;

	CPolygon * p1 = &poly;
	CPolygon * p2 = &rect;
	CPolygon * p3 = &trgl;

	p1->set_values(4, 5);
	p2->set_values(4, 5);
	p3->set_values(4, 5);

	printf("p1: %d\n", p1->area()); //0
	printf("p2: %d\n", p2->area()); //20
	printf("p3: %d\n", p3->area()); //10

	//CRectangle * p4 = &poly;
	CRectangle * p5 = &rect;
	//CRectangle * p6 = &trgl;

	p1->name(); //CPolygon
	p2->name(); //CPolygon
	p3->name(); //CPolygon

	return 0;
}
```

<a href="http://www.cnblogs.com/ggjucheng/archive/2012/01/02/2310487.html" target="_blank">http://www.cnblogs.com/ggjucheng/archive/2012/01/02/2310487.html</a>

