---
layout: post
title: "[openssl] base64编码"
date: 2017-01-11
author: mdgsf
comments: true
categories: openssl
tags: [Openssl,Base64]
description:
published: true #default true
---

```cpp
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include "openssl/bio.h"
#include "openssl/ssl.h"
#include "openssl/err.h"
#include "openssl/evp.h"
#include "openssl/buffer.h"

int main()
{
    char acInput[BUFSIZ] = {0};
    char acOutput[BUFSIZ] = {0};
    char acDecOut[BUFSIZ] = {0};
    strcpy(acInput, "hello");

    EVP_EncodeBlock((unsigned char*)acOutput, (const unsigned char*)acInput, strlen(acInput));

    EVP_DecodeBlock((unsigned char*)acDecOut, (const unsigned char*)acOutput, strlen(acOutput));

    printf("acInput = %s\n", acInput);
    printf("acOutput = %s\n", acOutput);
    printf("acDecOut = %s\n", acDecOut);

    return 0;
}
```
