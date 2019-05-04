---
layout: post
title: "[Head First Design Pattern] Strategy Pattern"
date: 2017-08-14
author: mdgsf
comments: true
categories: DesignPattern
tags: [Strategy]
description:
published: true #default true
---

策略模式：定义了算法族，分别封装起来，让它们之间可以互相替换，此模式让算法的变化独立于使用算法的客户。

```c++
#include <stdio.h>
#include <iostream>
using namespace std;

class QuackBehavior
{
public:
    virtual void Quack() = 0;
};

class FlyBehavior
{
public:
    virtual void Fly() = 0;
};

class Duck
{
public:
    void Swim()
    {
        cout << "All duck can swim.\n";
    }

    virtual void Display() = 0;

    void PerformQuack()
    {
        m_pQuackBehavior->Quack();
    }

    void PerformFly()
    {
        m_pFlyBehavior->Fly();
    }

    void SetQuackBehavior(QuackBehavior * pQuackBehavior)
    {
        m_pQuackBehavior = pQuackBehavior;
    }

    void SetFlyBehavior(FlyBehavior * pFlyBehavior)
    {
        m_pFlyBehavior = pFlyBehavior;
    }

private:
    QuackBehavior * m_pQuackBehavior;
    FlyBehavior * m_pFlyBehavior;
};

class SqueakDuck : public Duck
{
    void Display()
    {
        cout << "This is a squeak duck.\n";
    }
};

class SqueakQuackBehavior : public QuackBehavior
{
public:
    void Quack()
    {
        cout << "zi zi zi ...\n";
    }
};

class SqueakFlyBehavior : public FlyBehavior
{
public:
    void Fly()
    {
        cout << "Can't fly.\n";
    }
};

int main()
{
    Duck * d = new SqueakDuck;

    d->SetFlyBehavior(new SqueakFlyBehavior);
    d->SetQuackBehavior(new SqueakQuackBehavior);

    d->PerformFly();
    d->PerformQuack();

    return 0;
}
```
