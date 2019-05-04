---
layout: post
title: "[openssl] sha1,sha256,sha512"
date: 2017-01-11
author: mdgsf
comments: true
categories: openssl
tags: [Openssl,Sha]
description:
published: true #default true
---

```cpp
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include "openssl/sha.h"
#include "openssl/crypto.h"

const char * str = "hello";

void printHash(unsigned char * md, int iLen)
{
    for (int i = 0; i < iLen; i++)
    {
        printf("%02x", md[i]);
    }
    printf("\n");
}

void myHash1()
{
    unsigned char md[SHA_DIGEST_LENGTH] = {0};
    SHA1((unsigned char *)str, strlen(str), md);
    printHash(md, SHA_DIGEST_LENGTH);

    SHA_CTX c;
    SHA1_Init(&c);
    SHA1_Update(&c, str, strlen(str));
    SHA1_Final(md, &c);
    OPENSSL_cleanse(&c, sizeof(c));
    printHash(md, SHA_DIGEST_LENGTH);
    printf("\n");
}

void myHash224()
{
    unsigned char md[SHA224_DIGEST_LENGTH] = {0};
    SHA224((unsigned char *)str, strlen(str), md);
    printHash(md, SHA224_DIGEST_LENGTH);

    SHA256_CTX c;
    SHA224_Init(&c);
    SHA224_Update(&c, str, strlen(str));
    SHA224_Final(md, &c);
    OPENSSL_cleanse(&c, sizeof(c));
    printHash(md, SHA224_DIGEST_LENGTH);
    printf("\n");
}

void myHash256()
{
    unsigned char md[SHA256_DIGEST_LENGTH] = {0};
    SHA256((unsigned char *)str, strlen(str), md);
    printHash(md, SHA256_DIGEST_LENGTH);

    SHA256_CTX c;
    SHA256_Init(&c);
    SHA256_Update(&c, str, strlen(str));
    SHA256_Final(md, &c);
    OPENSSL_cleanse(&c, sizeof(c));
    printHash(md, SHA256_DIGEST_LENGTH);
    printf("\n");
}

void myHash384()
{
    unsigned char md[SHA384_DIGEST_LENGTH] = {0};
    SHA384((unsigned char *)str, strlen(str), md);
    printHash(md, SHA384_DIGEST_LENGTH);

    SHA512_CTX c;
    SHA384_Init(&c);
    SHA384_Update(&c, str, strlen(str));
    SHA384_Final(md, &c);
    OPENSSL_cleanse(&c, sizeof(c));
    printHash(md, SHA384_DIGEST_LENGTH);
    printf("\n");
}

void myHash512()
{
    unsigned char md[SHA512_DIGEST_LENGTH] = {0};
    SHA512((unsigned char *)str, strlen(str), md);
    printHash(md, SHA512_DIGEST_LENGTH);

    SHA512_CTX c;
    SHA512_Init(&c);
    SHA512_Update(&c, str, strlen(str));
    SHA512_Final(md, &c);
    OPENSSL_cleanse(&c, sizeof(c));
    printHash(md, SHA512_DIGEST_LENGTH);
    printf("\n");
}

int main()
{
    myHash1();
    myHash224();
    myHash256();
    myHash384();
    myHash512();
    return 0;
}
```
