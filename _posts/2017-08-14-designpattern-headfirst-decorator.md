---
layout: post
title: "[Head First Design Pattern] Decorator Pattern"
date: 2017-08-14
author: mdgsf
comments: true
categories: DesignPattern
tags: decorator
description:
published: true #default true
---

装饰者模式：被装饰的 和 装饰 要继承自同一个基类。


```c++
#include <iostream>
#include <string>
using namespace std;

class Beverge
{
public:
    virtual double Cost() = 0;

    virtual string GetDescription()
    {
        return m_description;
    }

    string m_description;
};

class CondimentDecorator : public Beverge
{
public:
    virtual string GetDescription() = 0;
};

class Espresso : public Beverge
{
public:
    Espresso() { m_description = "Espresso"; }
    double Cost() { return 1.99; }
};

class DarkRoast : public Beverge
{
public:
    DarkRoast() { m_description = "DarkRoast coffee"; }
    double Cost() { return 1.00; }
};

class HouseBlend : public Beverge
{
public:
    HouseBlend() { m_description = "HouseBlend coffee"; }
    double Cost() { return 0.89; }
};

class Mocha : public CondimentDecorator
{
public:
    Mocha(Beverge * pBeverge)
    {
        m_pBeverge = pBeverge;
    }

    string GetDescription()
    {
        return m_pBeverge->GetDescription() + ", Mocha ";
    }

    double Cost()
    {
        return 0.10 + m_pBeverge->Cost();
    }

private:
    Beverge * m_pBeverge;
};

class Whip : public CondimentDecorator
{
public:
    Whip(Beverge * pBeverge)
    {
        m_pBeverge = pBeverge;
    }

    string GetDescription()
    {
        return m_pBeverge->GetDescription() + ", Whip ";
    }

    double Cost()
    {
        return 0.20 + m_pBeverge->Cost();
    }

private:
    Beverge * m_pBeverge;
};

class Soy : public CondimentDecorator
{
public:
    Soy(Beverge * pBeverge)
    {
        m_pBeverge = pBeverge;
    }

    string GetDescription()
    {
        return m_pBeverge->GetDescription() + ", Soy ";
    }

    double Cost()
    {
        return 0.30 + m_pBeverge->Cost();
    }

private:
    Beverge * m_pBeverge;
};


int main()
{
    Beverge * pBeverge = new Espresso;
    cout << pBeverge->GetDescription() << " $" << pBeverge->Cost() << endl;

    Beverge * pBeverge2 = new Espresso;
    pBeverge2 = new Mocha(pBeverge2);
    pBeverge2 = new Whip(pBeverge2);
    pBeverge2 = new Soy(pBeverge2);
    cout << pBeverge2->GetDescription() << " $" << pBeverge2->Cost() << endl;

    Beverge * pBeverge3 = new Espresso;
    pBeverge3 = new Mocha(pBeverge3);
    pBeverge3 = new Whip(pBeverge3);
    pBeverge3 = new Whip(pBeverge3);
    cout << pBeverge3->GetDescription() << " $" << pBeverge3->Cost() << endl;
    return 0;
}
```

