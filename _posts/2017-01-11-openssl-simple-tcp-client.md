---
layout: post
title: "[openssl] Simple tcp client--Block and NonBlock read"
date: 2017-01-11
author: mdgsf
comments: true
categories: openssl
tags: openssl, sslclient
description:
published: true #default true
---


### Block read

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

void ShowCerts(SSL * ssl)
{
    X509 * cert = SSL_get_peer_certificate(ssl);
    if(NULL == cert) {
        printf("no cert\n");
        return ;
    }

    printf("cert information:\n");
    char * line = X509_NAME_oneline(X509_get_subject_name(cert), 0, 0);
    printf("%s\n", line);
    free(line);

    line = X509_NAME_oneline(X509_get_issuer_name(cert), 0, 0);
    printf("%s\n", line);
    free(line);

    X509_free(cert);
}

int main()
{
    SSL_library_init();
    OpenSSL_add_all_algorithms();
    SSL_load_error_strings();

    SSL_CTX * ctx = SSL_CTX_new(SSLv23_client_method());
    if(NULL == ctx) {
        ERR_print_errors_fp(stdout);
        exit(1);
    }

    int iSockFD = socket(AF_INET, SOCK_STREAM, 0);
    if(iSockFD <= 0) {
        perror("socket");
        exit(1);
    }

    struct sockaddr_in stServerAddr;
    stServerAddr.sin_family = AF_INET;
    stServerAddr.sin_port = htons(PORT);
    if(inet_aton(IP, (struct in_addr*)&stServerAddr.sin_addr.s_addr) == 0) {
        perror("inet_aton");
        exit(1);
    }

    if(connect(iSockFD, (struct sockaddr*)&stServerAddr, sizeof(stServerAddr)) != 0) {
        perror("connect");
        exit(1);
    }

    SSL * ssl = SSL_new(ctx);
    SSL_set_fd(ssl, iSockFD);

    if(SSL_connect(ssl) == -1)
    {
        ERR_print_errors_fp(stderr);
    }
    else
    {
        ShowCerts(ssl);
    }

    char acBuf[BUFSIZ] = {0};
    socklen_t iLen = SSL_read(ssl, acBuf, sizeof(acBuf));
    if(iLen <= 0) {
        printf("SSL_read failed\n");
        goto finish;
    } else {
        printf("SSL_read success, acBuf = %s\n", acBuf);
    }
    
    memset(acBuf, 0, sizeof(acBuf));
    strcpy(acBuf, "from client->server");
    iLen = SSL_write(ssl, acBuf, strlen(acBuf));
    if(iLen <= 0) {
        printf("SSL_write failed\n");
    } else {
        printf("SSL_write success\n");
    }

finish:
    SSL_shutdown(ssl);
    SSL_free(ssl);
    close(iSockFD);
    SSL_CTX_free(ctx);

    return 0;
}
```



### Non-block read



```cpp
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <errno.h>
#include <netinet/in.h>
#include <sys/types.h>
#include <sys/socket.h>
#include <sys/time.h>
#include <sys/wait.h>
#include <sys/select.h>
#include <unistd.h>
#include <fcntl.h>
#include <arpa/inet.h>
#include "openssl/bio.h"
#include "openssl/ssl.h"
#include "openssl/err.h"

#define IP "127.0.0.1"
#define PORT 8383

#define TCP_TIMEOUT 2

static inline void itimeofday(long *sec, long *usec)
{
    #if defined(__unix)
    struct timeval time;
    gettimeofday(&time, NULL);
    if (sec) *sec = time.tv_sec;
    if (usec) *usec = time.tv_usec;
    #else
    static long mode = 0, addsec = 0;
    BOOL retval;
    static unsigned long long freq = 1;
    unsigned long long qpc;
    if (mode == 0) {
        retval = QueryPerformanceFrequency((LARGE_INTEGER*)&freq);
        freq = (freq == 0)? 1 : freq;
        retval = QueryPerformanceCounter((LARGE_INTEGER*)&qpc);
        addsec = (long)time(NULL);
        addsec = addsec - (long)((qpc / freq) & 0x7fffffff);
        mode = 1;
    }
    retval = QueryPerformanceCounter((LARGE_INTEGER*)&qpc);
    retval = retval * 2;
    if (sec) *sec = (long)(qpc / freq) + addsec;
    if (usec) *usec = (long)((qpc % freq) * 1000000 / freq);
    #endif
}

unsigned long long iclock64(void)
{
    long s, u;
    unsigned long long value;
    itimeofday(&s, &u);
    value = ((unsigned long long)s) * 1000 + (u / 1000);
    return value;
}

unsigned int iclock()
{
    return (unsigned int)(iclock64() & 0xfffffffful);
}

void vTcpSetNonBlock(int iSocket)
{
    int opts;
    opts = fcntl(iSocket, F_GETFL);
    if(opts < 0) {
        exit(1);
    }

    opts = (opts | O_NONBLOCK);
    if(fcntl(iSocket, F_SETFL, opts) < 0) {
        exit(1);
    }
}

int iReadTimeout(
    SSL * ssl, 
    int iSocket, 
    char * pcBuf, 
    int * piBufLen,
    int iTimeout)
{
    if(NULL == ssl || iSocket <= 0 || NULL == pcBuf || NULL == piBufLen) {
        printf("iReadTimeout invlaid parameters\n");
        return EXIT_FAILURE;
    }

    struct timeval stTimeInterval;
    struct timeval stTimeIntervalTemp;
    stTimeInterval.tv_sec = 0;
    stTimeInterval.tv_usec = 10 * 1000;

    int iOnce = 0;
    int iError = 0;

    unsigned int uiCurrentTime = iclock();
    unsigned int uiEndTime;
    if(iTimeout <= 0) uiEndTime = uiCurrentTime + 10 * 1000; //10 seconds
    else uiEndTime = uiCurrentTime + iTimeout;

    while ( (uiCurrentTime = iclock()) < uiEndTime )
    {
        stTimeIntervalTemp = stTimeInterval;
        fd_set stReadSet;
        FD_ZERO(&stReadSet);
        FD_SET(iSocket, &stReadSet);
        iOnce = select(iSocket+1, &stReadSet, NULL, NULL, &stTimeIntervalTemp);
        if(1 == iOnce && FD_ISSET(iSocket, &stReadSet))
        {
            do {
                iOnce = SSL_read(ssl, pcBuf, *piBufLen);
                iError = SSL_get_error(ssl, iOnce);
                switch(iError)
                {
                case SSL_ERROR_NONE:
                    {
                        return EXIT_SUCCESS;
                    }
                    break;
                case SSL_ERROR_ZERO_RETURN:
                    {
                        //peer close the socket.
                        SSL_shutdown(ssl);
                        SSL_free(ssl);
                        close(iSocket);
                        return EXIT_FAILURE;
                    }
                    break;
                case SSL_ERROR_WANT_READ:
                    {
                        //continue to read.
                    }
                    break;
                default:
                    {
                        printf("SSL_read unknown error = %d\n", iError);
                        return EXIT_FAILURE;
                    }
                    break;
                }
            } while(SSL_pending(ssl) && (uiCurrentTime = iclock()) < uiEndTime );
        }
        else if(iOnce == 0)
        {
            continue;
        }
        return EXIT_FAILURE;
    }

    return TCP_TIMEOUT;
}

void ShowCerts(SSL * ssl)
{
    X509 * cert = SSL_get_peer_certificate(ssl);
    if(NULL == cert) {
        printf("no cert\n");
        return ;
    }

    printf("cert information:\n");
    char * line = X509_NAME_oneline(X509_get_subject_name(cert), 0, 0);
    printf("%s\n", line);
    free(line);

    line = X509_NAME_oneline(X509_get_issuer_name(cert), 0, 0);
    printf("%s\n", line);
    free(line);

    X509_free(cert);
}

int main()
{
    SSL_library_init();
    OpenSSL_add_all_algorithms();
    SSL_load_error_strings();

    SSL_CTX * ctx = SSL_CTX_new(SSLv23_client_method());
    if(NULL == ctx) {
        ERR_print_errors_fp(stdout);
        exit(1);
    }

    int iSockFD = socket(AF_INET, SOCK_STREAM, 0);
    if(iSockFD <= 0) {
        perror("socket");
        exit(1);
    }

    struct sockaddr_in stServerAddr;
    stServerAddr.sin_family = AF_INET;
    stServerAddr.sin_port = htons(PORT);
    if(inet_aton(IP, (struct in_addr*)&stServerAddr.sin_addr.s_addr) == 0) {
        perror("inet_aton");
        exit(1);
    }

    if(connect(iSockFD, (struct sockaddr*)&stServerAddr, sizeof(stServerAddr)) != 0) {
        perror("connect");
        exit(1);
    }

    SSL * ssl = SSL_new(ctx);
    SSL_set_fd(ssl, iSockFD);

    if(SSL_connect(ssl) == -1)
    {
        ERR_print_errors_fp(stderr);
    }
    else
    {
        ShowCerts(ssl);
    }

    char acBuf[BUFSIZ] = {0};
    socklen_t iLen = 0;

    //socklen_t iLen = SSL_read(ssl, acBuf, sizeof(acBuf));
    //if(iLen <= 0) {
    //  printf("SSL_read failed\n");
    //  goto finish;
    //} else {
    //  printf("SSL_read success, acBuf = %s\n", acBuf);
    //}


    vTcpSetNonBlock(iSockFD);

    int iBufLen = sizeof(acBuf);
    int iRet = iReadTimeout(ssl, iSockFD, acBuf, &iBufLen, 1000);
    if(iRet != EXIT_SUCCESS) {
        printf("iReadTimeout failed, iRet = %d\n", iRet);
        return -1;
    }
    printf("read success, acBuf = %s\n", acBuf);

    
    memset(acBuf, 0, sizeof(acBuf));
    strcpy(acBuf, "from client->server");
    iLen = SSL_write(ssl, acBuf, strlen(acBuf));
    if(iLen <= 0) {
        printf("SSL_write failed\n");
    } else {
        printf("SSL_write success\n");
    }


    SSL_shutdown(ssl);
    SSL_free(ssl);
    close(iSockFD);
    SSL_CTX_free(ctx);

    return 0;
}
```

