---
layout: post
title:  "[C/C++] vector 排序"
date:   2017-03-13
comments: true
categories: C/C++
tags: c, strcat
description:
published: true
---


```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

struct ss
{
    int a;
    int b;
};

bool ss_comp(const ss & sa, const ss & sb)
{
    return sa.a < sb.a;
}

bool comp(const int & a, const int & b)
{
    return a > b;
}

int main(int argc, char *argv[])
{
    cout << "Hello World!" << endl;

    vector<int> v;
    v.push_back(13);
    v.push_back(23);
    v.push_back(03);
    v.push_back(233);
    v.push_back(113);

    printf("after sort: little-->big\n");
    sort(v.begin(), v.end());
    for (int i = 0; i < 5; i++)
    {
        cout << v[i] << " ";
    }
    cout << endl;


    printf("after sort: big-->little\n");
    sort(v.begin(), v.end(), comp);
    for (int i = 0; i < 5; i++)
    {
        cout << v[i] << " ";
    }
    cout << endl;

    vector<ss> sv;
    ss s1 = {4, 23};
    ss s2 = {1, 213};
    ss s3 = {2, 231};
    ss s4 = {5, 123};
    ss s5 = {3, 223};
    sv.push_back(s1);
    sv.push_back(s2);
    sv.push_back(s3);
    sv.push_back(s4);
    sv.push_back(s5);
    printf("sort vector struct\n");
    sort(sv.begin(), sv.end(), ss_comp);
    for (int i = 0; i < 5; i++)
    {
        cout << sv[i].a << " " << sv[i].b << endl;
    }
    cout << endl;

    return 0;
}
```



