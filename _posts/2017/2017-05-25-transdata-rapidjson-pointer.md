---
layout: post
title:  "[json] rapidjson pointer"
date:   2017-05-25
comments: true
categories: transdata
tags: [Json]
description:
published: true
---

## 链接 

<a href="http://rapidjson.org/zh-cn/md_doc_pointer_8zh-cn.html" target="_blank">JSON Pointer</a>


```cpp
#include "rapidjson/document.h"
#include "rapidjson/prettywriter.h"
#include "rapidjson/pointer.h"
#include <cstdio>

using namespace rapidjson;
using namespace std;


void ShowJSON(Document & d)
{
    StringBuffer sb;
    PrettyWriter<StringBuffer> writer(sb);
    d.Accept(writer);
    puts(sb.GetString());
}

void objDemo()
{
    Document d;

    Pointer("/project").Set(d, "RapidJSON");
    Pointer("/stars").Set(d, 10);
    ShowJSON(d);

    if (Value * stars = Pointer("/stars").Get(d))
        stars->SetInt(stars->GetInt() + 1);
    ShowJSON(d);

    Pointer("/a/b/0").Create(d);
    ShowJSON(d);

    Value & hello = Pointer("/hello").GetWithDefault(d, "world");
    ShowJSON(d);


    Value x("C++");
    Pointer("/hello").Swap(d, x);
    ShowJSON(d);


    bool success = Pointer("/a").Erase(d);
    assert(success);
    ShowJSON(d);
}

void cDemo()
{
    Document d;
    SetValueByPointer(d, "/project", "RapidJSON");
    SetValueByPointer(d, "/stars", 10);
    ShowJSON(d);

    if (Value * stars = GetValueByPointer(d, "/stars"))
        stars->SetInt(stars->GetInt() + 1);

    CreateValueByPointer(d, "/a/b/0");

    Value & hello = GetValueByPointerWithDefault(d, "/hello", "world");
    ShowJSON(d);

    Value x("C++");
    SwapValueByPointer(d, "/hello", x);

    bool success = EraseValueByPointer(d, "/a");
    assert(success);

    ShowJSON(d);

    CreateValueByPointer(d, "/1/0"); 
    EraseValueByPointer(d, "/1/0");
    SetValueByPointer(d, "/1/-", 123);
    ShowJSON(d);

    SetValueByPointer(d, "/1/a", 789);
    ShowJSON(d);
}

int main()
{
    cDemo();
    
    return 0;
}
```
