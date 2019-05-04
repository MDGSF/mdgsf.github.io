---
layout: post
title:  "[protobuf] protobuf在windows下编译使用"
date:   2017-09-12
comments: true
categories: transdata
tags: [Protobuf]
description:
published: true
---

## 1. 链接

https://github.com/google/protobuf

下载下面的3个压缩包。

protobuf-cpp-3.4.0.tar.gz

protobuf-cpp-3.4.0.zip

protoc-3.4.0-win32.zip

protobuf-cpp-3.4.0.tar.gz 和 protobuf-cpp-3.4.0.zip 是一样的，都是源代码。

protoc-3.4.0-win32.zip 是一个自动生成代码的工具。

## 2.编译库

将 protobuf-cpp-3.4.0.zip 解压

在 protobuf-3.2.0\cmake 目录下创建子目录 build/solution

进入目录 protobuf-3.2.0\cmake\build\solution,

执行命令 cmake -G "Visual Studio 14 2015" ../..

下面这是一些其他的Visual Studio版本：

```
Visual Studio 15 2017 [arch] = Generates Visual Studio 2017 project files.
                                 Optional [arch] can be "Win64" or "ARM".
Visual Studio 14 2015 [arch] = Generates Visual Studio 2015 project files.
                               Optional [arch] can be "Win64" or "ARM".
Visual Studio 12 2013 [arch] = Generates Visual Studio 2013 project files.
                               Optional [arch] can be "Win64" or "ARM".
Visual Studio 11 2012 [arch] = Generates Visual Studio 2012 project files.
                               Optional [arch] can be "Win64" or "ARM".
Visual Studio 10 2010 [arch] = Generates Visual Studio 2010 project files.
                               Optional [arch] can be "Win64" or "IA64".
Visual Studio 9 2008 [arch]  = Generates Visual Studio 2008 project files.
                               Optional [arch] can be "Win64" or "IA64".
Visual Studio 8 2005 [arch]  = Generates Visual Studio 2005 project files.
                               Optional [arch] can be "Win64".
Visual Studio 7 .NET 2003    = Deprecated.  Generates Visual Studio .NET
```

然后就会在 solution 目录下生成 Visual Studio 的工程。

## 3. 使用

新建Message.proto文件

```
syntax = "proto2";

package tutorial;

message Person {
    optional string name = 1;
    optional int32 id = 2;
    optional string email = 3;

    enum PhoneType {
        MOBILE = 0;
        HOME = 1;
        WORK = 2;
    }

    message PhoneNumber {
        optional string number = 1;
        optional PhoneType type = 2 [default = HOME];
    }

    repeated PhoneNumber phone = 4;
}


message AddressBook {
    repeated Person person = 1;
}
```

执行命令：

```
protoc -I=./ --cpp_out=./ Message.proto
```

然后会自动生成 Message.pb.h 和 Message.pb.cc 这两个文件。

这是测试文件：

```c++
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <iostream>
#include <string>
#include "Message.pb.h"

using namespace std;

int main()
{
    printf("ProtoBuf Demo\n");

    GOOGLE_PROTOBUF_VERIFY_VERSION;


    tutorial::AddressBook oAddressBook;

    printf("oAddressBook.person_size() = %d\n", oAddressBook.person_size());

    ::tutorial::Person* poPerson = oAddressBook.add_person();
    poPerson->set_id(1);
    poPerson->set_name("Person 1");
    poPerson->set_email("person1@qq.com");

    ::tutorial::Person_PhoneNumber* poPhoneNumberHome = poPerson->add_phone();
    poPhoneNumberHome->set_number("12345678");
    poPhoneNumberHome->set_type(::tutorial::Person::HOME);
    poPhoneNumberHome->set_type(::tutorial::Person_PhoneType_HOME);

    ::tutorial::Person_PhoneNumber* poPhoneNumberWork = poPerson->add_phone();
    poPhoneNumberWork->set_number("87654321");
    poPhoneNumberWork->set_type(::tutorial::Person::WORK);
    //poPhoneNumberWork->set_type(::tutorial::Person_PhoneType_WORK);

    printf("poPerson->phone_size() = %d\n", poPerson->phone_size());

    printf("oAddressBook.person_size() = %d\n", oAddressBook.person_size());


    string strOutput;
    oAddressBook.SerializePartialToString(&strOutput);
    printf("strOutput: [%s]\n", strOutput.c_str());


    tutorial::AddressBook oAddressBookOutput;
    oAddressBookOutput.ParseFromString(strOutput);
    printf("oAddressBookOutput.person_size() = %d\n", oAddressBookOutput.person_size());


    google::protobuf::ShutdownProtobufLibrary();

    return 0;
}
```
