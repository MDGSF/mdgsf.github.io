---
layout: post
title:  "[C/C++] 复习题"
date:   2016-12-28
comments: true
categories: C/C++
tags: [C]
description: ""
published: true
---




```cpp
11. 假设ClassY:publicX，即类Y是类X的派生类，则说明一个Y类的对象时和删除Y类对象时
，调用构造函数和析构函数的次序分别为（A）
A. X,Y；Y,X
B. X,Y；X,Y
C. Y,X；X,Y
D. Y,X；Y,X
```


```cpp
15. 下列不能作为类的成员的是（B）
A. 自身类对象的指针
B. 自身类对象
C. 自身类对象的引用
D. 另一个类的对象
答案：B
解析：类的定义，如果有自身类对象，使得循环定义，B项错误。在类中具有自身类的指针，可
以实现链表的操作，当然也可以使用对象的引用。类中可以有另一个类的对象，即成员对象。所
以选择B选项。
```


```cpp
17.下列程序的输出结果是（）
#include <iostream.h>
void main()
{int n［］［3］={10,20,30,40,50,60};
int (*p)［3］;
p=n;
cout<<p［0］［0］<<"，"<<*(p［0］+1)<<"，"<<(*p)［2］<<endl;}
A. 10，30，50
B. 10，20，30
C. 20，40，60
D. 10，30，60
```


```cpp
9. 对赋值运算符进行重载时，应声明为__类成员_函数。
答案：(P183)类成员
［解析］运算符重载的方法有友元或者成员函数两种途径，但是赋值运算符只能使用成员函数的
方法来实现。
```


```cpp
10. 如果要把A类成员函数f（）且返回值为void声明为类B的友元函数，则应在类B的定义中加
入的语句_friend void A::f()__。
答案：(P109)friend void A::f()；
［解析］成员函数作为另一个类的友元函数，格式为：friend 返回类型 类名::函数(形参)。
```


```cpp
14. 控制格式输入输出的操作中，函数___是用来设置填充字符。要求给出函数名和参数类型
答案：(P195)setfill(char)
［解析］格式控制方法的使用，如setw，setfill等等。
```


```cpp
15. C++语言支持的两种多态性分别是编译时的多态性和__运行时_的多态性。
答案：(P167)运行时
［解析］多态性包括静态的（编译时）多态性和动态的（运行时）多态性。
```


```cpp
17. 执行下列代码
string str("HelloC++");
cout<<str.substr(5，3);
程序的输出结果是__C++_。
答案：(P42)C++
［解析］substr取子字符串，第1个参数表示要截取子串在字符串中的位置，第2个表示取多少个
字符。
```


```cpp
19. 定义类动态对象数组时，元素只能靠自动调用该类的_无参数构造函数__来进行初始化。
答案：(P77)无参构造函数
［解析］使用new 创建动态对象数组，不能有参数，所以只能调用无参的构造函数，初始化对象
```


```cpp
20. 已知有20个元素int类型向量V1，若用V1初始化为V2向量，语句是___。
答案：(P151)ector <int>V2(V1);
［解析］采用向量初始化另一个向量的形式：vector <type> name1(name);
```


```cpp
3. #include <iostream.h>
class A
{
	int i;
public:
	virtual void fun()=0;
	A(int a)
	{i=a;}
};
class B:public A
{
	int j;
public:
	void fun()
	{cout<<"B::fun()＼n"; }
	B(int m,int n=0):A(m),j(n){}
};
void main()
{
	A *pa;
	B b(7);
	pa=&b;
}
答案：B(int m,int n=0):A(m),j(n){}因为基类是抽象类，不能被实例化，所以在派生类中不能
调用初始化基类对象。所以B(int m,int n=0):A(m),j(n){}错误，删去A(m)。
［修改］B(int m,int n=0):j(n){}
```


```cpp
1. 给出下面程序输出结果。
#include<iostream.h>
class a
{public:
virtual void print()
{cout<< "a prog..."<< endl;};
};
class b:public a
{};
class c:public b
{public:
void print(){cout<<"c prog..."<<endl;}
};
void show(a *p)
{(*p).print();
}
void main()
{a a;
b b;
c c;
show(&a);a prog…
show(&b);a prog…
show(&c);c prog
}
答案：a prog...
a prog...
c prog...
［解析］考查多态性的。a类对象调用本身的虚函数，b类因为没有覆写print，所以仍然调用基
类的虚函数。而c类重新定义print虚函数，所以调用c类的print。
```


```cpp
A * p = new A;
A * p = new B;

class A {
	void fun() {};
}

clas B : public A {
	void fun() {};
}

有virtual， p->fun() 取决于右边
没有virtual， p->fun() 取决于左边

```



























































