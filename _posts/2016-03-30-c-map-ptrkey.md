---
layout: post
title:  "[STL] map 中用char*作为key"
date:   2016-03-30
comments: true
categories: c
tags: c++, map
description:
published: true
---

```cpp
#include <iostream>
#include <map>
using namespace std;
struct cmp
{
    bool operator()(const char * pc1, const char * pc2) const
    {
        return strcmp(pc1, pc2)<0;
    }
};
int main()
{
    map<char *, int, cmp> myMap;
    cout<<myMap.size()<<endl;
    int i;
    for ( i=0 ; i<10; i++)
    {
        char * acStr = new char[50];
        strcpy(acStr, "huangjian");
        char acNum[50] = {0};
        itoa(i, acNum, 10);
        strcat(acStr, acNum);
        cout<<acStr<<endl;
        myMap.insert(std::pair<char *, int>(acStr, i));
    }
    cout<<myMap.size()<<endl;
    map<char *, int, cmp>::iterator it;
    char acF[50] = "huangjian9";
    char * pc = acF;
    it = myMap.find(pc);
    if( myMap.end() == it )
    {
        cout<<"end"<<endl;
    }
    cout<<it->first<<" "<<it->second <<endl;
    for ( it=myMap.begin(); it != myMap.end(); it++)
    {
        cout<<it->first<<" "<<it->second<<endl;
    }
    cout<<endl<<endl;
    map<const char *, int, cmp> cMap;
    cMap.insert(std::pair<const char *, int>("huangjian1", 1));
    cMap.insert(std::pair<const char *, int>("huangjian2", 2));
    cMap.insert(std::pair<const char *, int>("huangjian3", 3));
    cMap.insert(std::pair<const char *, int>("huangjian4", 4));
    cMap.insert(std::pair<const char *, int>("huangjian5", 5));

    map<const char *, int, cmp>::iterator itc;
    for (itc=cMap.begin(); itc != cMap.end(); itc++)
    {
        cout<<itc->first<<" "<<itc->second<<endl;
    }
    getchar();
    return 0;
}
```
