---
layout: post
title: "[openssl] Simple tcp server"
date: 2017-01-11
author: mdgsf
comments: true
categories: openssl
tags: [Openssl,Server]
description:
published: true #default true
---


### Generate private key and cacert.pem

> To generate RSA private key, 2048 bit.
> **openssl genrsa -out privatekey.pem**
> 
> To generate RSA public key
> **openssl rsa -in privatekey.pem -pubout -out publickey.pem**
> 
> chage private key to pkcs8 format:
> **openssl pkcs8 -topk8 -inform PEM -in privatekey.pem -outform PEM -nocrypt**
> 
> 
> To create a hex-encoded message digest of a file:
> **openssl dgst -md5 -hex file.txt**
> 
> To sign a file using SHA-256 with binary file output:
> **openssl dgst -sha256 -sign privatekey.pem -out signature.sign file.txt**
> 
> To verify a signature:
> **openssl dgst -sha256 -verify publickey.pem -signature signature.sign file.txt**


### Code

```cpp
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <errno.h>
#include <netinet/in.h>
#include <sys/types.h>
#include <sys/socket.h>
#include <sys/wait.h>
#include <unistd.h>
#include <arpa/inet.h>
#include "openssl/bio.h"
#include "openssl/ssl.h"
#include "openssl/err.h"

#define IP "127.0.0.1"
#define PORT 8383
#define FILE_CACERT "cacert.pem"
#define FILE_PRIVATE_KEY "privatekey.pem"

int main()
{
    SSL_library_init();
    OpenSSL_add_all_algorithms();
    SSL_load_error_strings();

    SSL_CTX * ctx = SSL_CTX_new(SSLv23_server_method());
    if(NULL == ctx) {
        ERR_print_errors_fp(stdout);
        exit(1);
    }

    if(SSL_CTX_use_certificate_file(ctx, FILE_CACERT,  SSL_FILETYPE_PEM) <= 0) {
        ERR_print_errors_fp(stdout);
        exit(1);
    }

    if(SSL_CTX_use_PrivateKey_file(ctx, FILE_PRIVATE_KEY, SSL_FILETYPE_PEM) <= 0) {
        ERR_print_errors_fp(stdout);
        exit(1);
    }

    if(!SSL_CTX_check_private_key(ctx)) {
        ERR_print_errors_fp(stdout);
        exit(1);
    }

    int iSockFD = socket(AF_INET, SOCK_STREAM, 0);
    if(iSockFD == -1) {
        perror("socket");
        exit(1);
    }

    struct sockaddr_in stServerAddr;
    memset(&stServerAddr, 0, sizeof(stServerAddr));
    stServerAddr.sin_family = AF_INET;
    stServerAddr.sin_port = htons(PORT);
    stServerAddr.sin_addr.s_addr = inet_addr(IP);

    if(bind(iSockFD, (struct sockaddr*)&stServerAddr, sizeof(struct sockaddr)) == -1) {
        perror("bind");
        exit(1);
    }

    if(listen(iSockFD, 5) == -1) {
        perror("listen");
        exit(1);
    }

    while (1) 
    {
        socklen_t iLen = sizeof(struct sockaddr);
        struct sockaddr_in stClientAddr;
        int iNewFD = accept(iSockFD, (struct sockaddr*)&stClientAddr, &iLen);
        if(iNewFD == -1) {
            perror("accept");
            exit(1);
        }
        printf("%s:%d, socket %d\n", 
                inet_ntoa(stClientAddr.sin_addr), 
                ntohs(stClientAddr.sin_port),
                iNewFD);

        SSL * ssl = SSL_new(ctx);
        SSL_set_fd(ssl, iNewFD);
        if(SSL_accept(ssl) == -1) {
            perror("SSL_accept");
            close(iNewFD);
            break;
        }

        char acBuf[BUFSIZ] = {0};
        strcpy(acBuf, "server->client");

        sleep(2);

        iLen = SSL_write(ssl, acBuf, strlen(acBuf));
        if(iLen <= 0) {
            printf("SSL_write failed\n");
            goto finish;
        }

        memset(acBuf, 0, sizeof(acBuf));
        iLen = SSL_read(ssl, acBuf, sizeof(acBuf));
        if(iLen <= 0) {
            printf("SSL_read failed\n");
        } else {
            printf("SSL_read success, acBuf = %s\n", acBuf);
        }
                

finish:
        SSL_shutdown(ssl);
        SSL_free(ssl);
        close(iNewFD);
    }

    close(iSockFD);
    SSL_CTX_free(ctx);

    return 0;
}
```



