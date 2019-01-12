---
layout: post
title:  "[json] rapidjson 基础使用"
date:   2017-05-25
comments: true
categories: transdata
tags: json
description:
published: true
---

## 链接 

<a href="http://json.org/" target="_blank">Introducing JSON</a>

<a href="http://www.ietf.org/rfc/rfc7159.txt" target="_blank">RFC7159: The JavaScript Object Notation (JSON) Data Interchange Format</a>

<a href="https://github.com/miloyip/rapidjson" target="_blank">rapidjson Github</a>

<a href="http://rapidjson.org/zh-cn/index.html" target="_blank">rapidjson 教程</a>


## Introduction

   JavaScript Object Notation (JSON) is a text format for the
   serialization of structured data.  It is derived from the object
   literals of JavaScript, as defined in the ECMAScript Programming
   Language Standard, Third Edition [ECMA-262].

   JSON can represent four primitive types (strings, numbers, booleans,
   and null) and two structured types (objects and arrays).

   A string is a sequence of zero or more Unicode characters [UNICODE].
   Note that this citation references the latest version of Unicode
   rather than a specific release.  It is not expected that future
   changes in the UNICODE specification will impact the syntax of JSON.

   An object is an unordered collection of zero or more name/value
   pairs, where a name is a string and a value is a string, number,
   boolean, null, object, or array.

   An array is an ordered sequence of zero or more values.


## 使用

RapidJSON 是只有头文件的 C++ 库。只需把 **include/rapidjson** 目录复制至系统或项目的 include 目录中。

在example目录下有很多使用的实例。

```cpp
#include "rapidjson/document.h"
#include "rapidjson/prettywriter.h"
#include <cstdio>

using namespace rapidjson;
using namespace std;

int main()
{
    const char json[] = " { \"hello\" : \"world\", \"t\" : true , \"f\" : false, \"n\": null, \"i\":123, \"pi\": 3.1416, \"a\":[1, 2, 3, 4] } ";
    printf("Original JSON:\n %s\n", json);

    Document document;
    char buffer[sizeof(json)];
    memcpy(buffer, json, sizeof(json));
    if (document.ParseInsitu(buffer).HasParseError())
        return 1;

    printf("\nParsing to docuemnt succeed.\n");

    printf("\nAccess values in document:\n");
    assert(document.IsObject());

    assert(document.HasMember("hello"));
    assert(document["hello"].IsString());
    printf("hello = %s\n", document["hello"].GetString());

    Value::MemberIterator hello = document.FindMember("hello");
    assert(hello != document.MemberEnd());
    assert(hello->value.IsString());
    assert(strcmp("world", hello->value.GetString()) == 0);
    (void)hello;

    assert(document["t"].IsBool());
    printf("t = %s\n", document["t"].GetBool() ? "true" : "false");

    assert(document["f"].IsBool());
    printf("f = %s\n", document["f"].GetBool() ? "true" : "false");

    printf("n = %s\n", document["n"].IsNull() ? "null" : "?");


    assert(document["i"].IsNumber());
    assert(document["i"].IsInt());
    printf("i = %d\n", document["i"].GetInt());

    assert(document["pi"].IsNumber());
    assert(document["pi"].IsDouble());
    printf("pi = %g\n", document["pi"].GetDouble());

    // Iterating array with iterators.
    {
        const Value & a = document["a"];
        assert(a.IsArray());
        for (SizeType i = 0; i < a.Size(); i++)
            printf("a[%d] = %d\n", i, a[i].GetInt());

        int y = a[0].GetInt();
        (void)y;

        printf("a = ");
        for (Value::ConstValueIterator itr = a.Begin(); itr != a.End(); ++itr)
            printf("%d ", itr->GetInt());
        printf("\n");
    }


    // Iterating object member.
    static const char * kTypeNames[] = {"Null","False","True","Object","Array","String","Number"};
    for (Value::ConstMemberIterator itr = document.MemberBegin(); itr != document.MemberEnd(); itr++)
        printf("Type of member %s is %s\n", itr->name.GetString(), kTypeNames[itr->value.GetType()]);


    // Change i to a bigger number
    {
        uint64_t f20 = 1;
        for (uint64_t j = 1; j <= 20; j++)
            f20 *= j;
        //document["i"] = f20;
        document["i"].SetUint64(f20);
        assert(!document["i"].IsInt());
        assert(document["i"].IsUint64());
    }

    // Adding values to array.
    {
        Value & a = document["a"];
        Document::AllocatorType & allocator = document.GetAllocator();
        for (int i = 5; i <= 10; i++)
            a.PushBack(i, allocator);

        a.PushBack("Lua", allocator).PushBack("Mio", allocator);
    }


    // string values
    {
        //document["hello"] = "rapidjson"; //This will invoke strlen()
        document["hello"].SetString("rapidjson", 9); //Faster version
    }

    // This version of SetString() needs an allocator, which means it will allocate a new buffer and copy the the string into the buffer.
    Value author;
    {
        char buffer2[10];
        int len = sprintf(buffer2, "%s %s", "Milo", "Yip");  // synthetic example of dynamically created string.

        author.SetString(buffer2, static_cast<SizeType>(len), document.GetAllocator());
        // Shorter but slower version:
        // document["hello"].SetString(buffer, document.GetAllocator());

        // Constructor version: 
        // Value author(buffer, len, document.GetAllocator());
        // Value author(buffer, document.GetAllocator());
        memset(buffer2, 0, sizeof(buffer2)); // For demonstration purpose.
    }
    // Variable 'buffer' is unusable now but 'author' has already made a copy.
    document.AddMember("author", author, document.GetAllocator());

    assert(author.IsNull());        // Move semantic for assignment. After this variable is assigned as a member, the variable becomes null.


    // Stringfy JSON
    printf("\nModified JSON with reformatting:\n");
    StringBuffer sb;
    PrettyWriter<StringBuffer> writer(sb);
    document.Accept(writer);
    puts(sb.GetString());

    return 0;
}
```



