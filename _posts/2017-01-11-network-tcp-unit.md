---
layout: post
title: "[Network] 常用tcp代码"
date: 2017-01-11
author: mdgsf
comments: true
categories: Network
tags: [Network,Tcp,Tcpunit]
description:
published: true #default true
---




### tcpunit.h

```cpp
#ifndef TCP_UNIT_WIN_HJ
#define TCP_UNIT_WIN_HJ

#ifdef WIN32

#pragma comment(lib, "iphlpapi.lib")
#pragma comment(lib, "ws2_32.lib")
#include <WinSock2.h>
#include <ws2tcpip.h>
#include <MSTcpIP.h>
#include <iphlpapi.h>

#else

#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <errno.h>
#include <sys/ioctl.h>
#include <sys/socket.h>
#include <sys/types.h>
#include <sys/ioctl.h>
#include <sys/wait.h>
#include <sys/epoll.h>
#include <sys/stat.h>
#include <arpa/inet.h>
#include <netinet/in.h>
#include <netinet/tcp.h>
#include <net/if.h>
#include <features.h>
#include <fcntl.h>

#endif


#include "common_def.h"
#include "utility.h"

/*
 *  @return 
 *      EXIT_SUCCESS: success.
 *      EXIT_FAILURE: failed.
 *      TCP_TIMEOUT: read or write timeout.
 *      TCP_EOF: peer close the connection.
 */

void tcp_SetNonblock(int iSocket);

void tcp_Setblock(int iSocket);

void tcp_vCloseSocket(int & riSocket);


/*
 *  @param piSocket[out]: if success, return socket descriptor.
 *  @param pcAddr[in]: ip address.
 *  @param usPort[in]: ip port.
 *  @param iTimeout[in]: Millisecond.
 */
int tcp_iConnect(int * piSocket, const char * pcAddr, unsigned short usPort, int iTimeout);


/*
 *  @param iSocket[in]: socket descriptor.
 *  @param pcBuf[out]: buffer used to recv data.
 *  @param piBufLen[in|out]: input the length of pcBuf, output the recvd length.
 */
int tcp_iRecvMsg(int iSocket, char * pcBuf, int * piBufLen);


/*
 *  @param iSocket[in]: socket descriptor.
 *  @param pcBuf[out]: buffer used to recv data.
 *  @param piBufLen[in|out]: 
 *                  input the size of data want to read.
 *                  if return EXIT_SUCCESS, it means read success,
 *                  if return TCP_TIMEOUT, piBufLen output the size had already read.
 *  @param iTimeout[in]: Millisecond.
 */
int tcp_iRecvMsgTimeout(int iSocket, char * pcBuf, int * piBufLen, int iTimeout);


/*
 *  @param iSocket[in]: socket descriptor.
 *  @param pcBuf[in]: 
 *  @param piBufLen[in|out]: input the size of data ready to send, output the really size of data send success.
 */
int tcp_iSendMsg(int iSocket, const char * pcBuf, int * piBufLen);


/*
 *  @param iSocket[in]: socket descriptor.
 *  @param pcBuf[in]: 
 *  @param iBufLen[in]: the size of data ready to send.
 */
int tcp_iSendAllMsg(int iSocket, const char * pcBuf, int iBufLen);


/*
 *  @param iSocket[in]: socket descriptor.
 *  @param pcBuf[in]: 
 *  @param iSize[in]: the size of data ready to send.
 *  @param iTimeout[in]: Millisecond. if your input <=0, it default use 100 milliseconds.
 */
int tcp_iSendMsgTimeout(int iSocket, const char * pcBuf, int iSize, int iTimeout);


#endif
```



<br />
<br />
<br />



### tcpunit_win.cpp

```cpp
#include "tcpunit.h"
#ifdef WIN32

void tcp_SetNonblock(int iSocket)
{
    unsigned long flags = 1;
    ioctlsocket(iSocket, FIONBIO, &flags);
}

void tcp_Setblock(int iSocket)
{
    unsigned long flags = 0;
    ioctlsocket(iSocket, FIONBIO, &flags);
}

void tcp_vCloseSocket(int & riSocket)
{
    if(riSocket != 0)
    {
        closesocket(riSocket);
        riSocket = 0;
    }
}

int 
tcp_iConnect(int *piSocket, const char * pcAddr, unsigned short usPort, int iTimeout)
{
    int iRet;

    if(NULL == piSocket || NULL == pcAddr || 0 == usPort) {
        printf("tcp_iConnect input error!\n");
        return EXIT_FAILURE;
    }

    int iSocket = socket(AF_INET, SOCK_STREAM, 0);
    if(iSocket < 0) {
        printf("tcp_iConnect socket error!\n");
        return EXIT_FAILURE;
    }

    int iTime = 2;
    setsockopt(iSocket, SOL_SOCKET, SO_RCVTIMEO, (char*)&iTime, sizeof(int));

    int iKeepAlive = 1;  //开启keepalive属性
    int iKeepIdle = 120; //如果连接在120秒内没有任何数据往来，则进行探测
    int iKeepInterval = 5;  //探测发包的时间间隔为5秒
    //int iKeepCount = 3;  //探测尝试的次数
    iRet = setsockopt(iSocket, SOL_SOCKET, SO_KEEPALIVE, (char*)&iKeepAlive, sizeof(iKeepAlive));
    if(iRet != 0) {
        printf("tcp_iConnect SO_KEEPALIVE error=%d\n", WSAGetLastError());
    }

    DWORD iByteNum;
    tcp_keepalive sPara = {1, iKeepIdle*1000, iKeepInterval*1000};
    iRet = WSAIoctl(iSocket, SIO_KEEPALIVE_VALS, &sPara, sizeof(sPara), &sPara, sizeof(sPara), &iByteNum,NULL,NULL);
    if(iRet != 0) {
        printf("tcp_iConnect WSAIoctl SIO_KEEPALIVE_VALS error=%d\n", WSAGetLastError());
    }


    tcp_SetNonblock(iSocket);

    struct sockaddr_in stAddr;
    memset(&stAddr, 0, sizeof(stAddr));
    stAddr.sin_family = AF_INET;
    stAddr.sin_port = htons(usPort);
    stAddr.sin_addr.s_addr = inet_addr(pcAddr);
    memset(stAddr.sin_zero, 0x00, 8);

    iRet = ::connect(iSocket, (struct sockaddr*)&stAddr, sizeof(stAddr));
    if(iRet < 0) 
    {
        DWORD dwError = GetLastError();
        if(dwError != WSAEINPROGRESS && dwError != WSAEWOULDBLOCK) {
            printf("tcp_iConnect connect error(%d)\n", GetLastError());
            tcp_vCloseSocket(iSocket);
            return EXIT_FAILURE;
        }
    }
    else if(iRet == 0) 
    {
        tcp_Setblock(iSocket);
        return EXIT_SUCCESS;
    }

    fd_set stReadSet;
    fd_set stWriteSet;
    FD_ZERO(&stReadSet);
    FD_SET(iSocket, &stReadSet);
    stWriteSet = stReadSet;

    struct timeval stTimeInterval;
    stTimeInterval.tv_sec = iTimeout / 1000;
    stTimeInterval.tv_usec = (iTimeout % 1000) * 1000;

    iRet = select(0, &stReadSet , &stWriteSet, NULL, &stTimeInterval);
    if(iRet <= 0) 
    {
        printf("tcp_iConnect select iRet=%d\n", iRet);
        tcp_vCloseSocket(iSocket);
        return EXIT_FAILURE;
    }

DONE:
    tcp_Setblock(iSocket);

    *piSocket = iSocket;

    return EXIT_SUCCESS;
}

int
tcp_iRecvMsg(int iSocket, char *pcBuf, int * piBufLen)
{
    if(iSocket <=0 || NULL == pcBuf || NULL == piBufLen || (*piBufLen < 0)) {
        printf("tcp_iRecvMsg input error!\n");
        return EXIT_FAILURE;
    }

    struct timeval stTimeInterval;
    stTimeInterval.tv_sec = 0;
    stTimeInterval.tv_usec = 100 * 1000;
    fd_set stReadSet;
    FD_ZERO(&stReadSet);
    FD_SET(iSocket, &stReadSet);
    int iActiveNum = select(iSocket + 1, &stReadSet, NULL, NULL, &stTimeInterval);
    if(iActiveNum == 1 && FD_ISSET(iSocket, &stReadSet))
    {
        int iRecv = recv(iSocket, pcBuf , *piBufLen, 0);
        if(iRecv > 0)
        {
            *piBufLen = iRecv;
            return EXIT_SUCCESS;
        }
        else if(iRecv == 0)
        {
            return TCP_EOF;
        }
    }
    else if(iActiveNum == 0)
    {
        return TCP_TIMEOUT;
    }
    return EXIT_FAILURE;
}


int
tcp_iRecvMsgTimeout(int iSocket, char * pcBuf, int * piBufLen, int iTimeout)
{
    if(iSocket <=0 || NULL == pcBuf || NULL == piBufLen || (*piBufLen <= 0)) {
        printf("tcp_iRecvMsgTimeout input error!\n");
        return EXIT_FAILURE;
    }  

    struct timeval stTimeInterval;
    struct timeval stTimeIntervalTemp;
    stTimeInterval.tv_sec = 0;
    stTimeInterval.tv_usec = 10 * 1000;

    int iOnce = 0;
    int iRecv = 0;
    char *pcIndex = pcBuf;

    unsigned int uiCurrentTime = iclock();
    unsigned int uiEndTime;
    if(iTimeout <= 0) uiEndTime = uiCurrentTime + 10 * 1000;
    else uiEndTime = uiCurrentTime + iTimeout * 1000;

    while ( (uiCurrentTime = iclock()) < uiEndTime )
    {
        stTimeIntervalTemp = stTimeInterval;
        fd_set stReadSet;
        FD_ZERO(&stReadSet);
        FD_SET(iSocket, &stReadSet);
        iOnce = select(iSocket + 1, &stReadSet , NULL, NULL, &stTimeIntervalTemp);
        if(1 == iOnce && FD_ISSET(iSocket, &stReadSet))
        {
            iOnce = recv(iSocket, pcIndex , *piBufLen - iRecv, 0);
            if(iOnce > 0)
            {
                iRecv += iOnce;
                if(iRecv >= *piBufLen)
                {
                    return EXIT_SUCCESS;
                }
                else
                {
                    pcIndex += iOnce;
                    continue;
                }
            }
            else if(iOnce == 0)
            {
                return TCP_EOF;
            }
        }
        else if(iOnce == 0)
        {
            continue;
        }
        return EXIT_FAILURE;
    }

    *piBufLen = iRecv;
    return TCP_TIMEOUT;
}


int 
tcp_iSendMsg(int iSocket, const char *pcBuf, int * piBufLen)
{
    if(iSocket <= 0 || NULL == pcBuf || NULL == piBufLen || (*piBufLen <= 0)) {
        printf("tcp_iSendMsg input error!\n");
        return EXIT_FAILURE;
    }

    int iSendLen = send(iSocket, pcBuf, *piBufLen, 0);
    if(iSendLen < 0) {
        printf("tcp_iSendMsg send error. return value = %d. \n",iSendLen);
        return EXIT_FAILURE;
    }

    *piBufLen = iSendLen;
    return EXIT_SUCCESS;
}

int 
tcp_iSendAllMsg(int iSocket, const char *pcBuf, int iBufLen)
{
    if(iSocket <= 0 || NULL == pcBuf || 0 == iBufLen) {
        printf("tcp_iSendAllMsg input error!\n");
        return EXIT_FAILURE;
    }

    int iSendLen = 0;
    while(iSendLen < iBufLen)
    {
        int iSendLenTmp = send(iSocket, pcBuf + iSendLen, iBufLen - iSendLen, 0);
        if(iSendLenTmp < 0) {
            printf("tcp_iSendAllMsg send error. return value = %d. \n",iSendLenTmp);
            return EXIT_FAILURE;
        }

        iSendLen += iSendLenTmp;
    }

    return EXIT_SUCCESS;
}


int 
tcp_iSendMsgTimeout(int iSocket, const char *pcBuf, int iSize, int iTimeout)
{
    if(iSocket <= 0 || NULL == pcBuf || iSize <= 0) {
        printf("tcp_iSendMsgTimeout input error!\n");
        return EXIT_FAILURE;
    }

    int iTotalWriteLen = 0;
    char * pcBufferIndex = (char *)pcBuf;

    unsigned int uiCurrentTime = iclock();
    unsigned int uiEndTime;
    if(iTimeout <= 0) uiEndTime = uiCurrentTime + 100 * 1000;
    else uiEndTime = uiCurrentTime + iTimeout * 1000;

    while((uiCurrentTime = iclock()) < uiEndTime)
    {
        struct timeval stTimeInterval;
        stTimeInterval.tv_sec = 0;
        stTimeInterval.tv_usec = 10 * 1000;

        fd_set stWriteSet;
        FD_ZERO(&stWriteSet);
        FD_SET(iSocket, &stWriteSet);
        int iOnce = select(iSocket + 1, NULL , &stWriteSet, NULL, &stTimeInterval);
        if(1 == iOnce && FD_ISSET(iSocket, &stWriteSet))
        {
            int iWriteLen = send(iSocket, pcBufferIndex, iSize - iTotalWriteLen, 0);
            if(iWriteLen > 0)
            {
                iTotalWriteLen += iWriteLen;
                if(iTotalWriteLen >= iSize)
                {
                    /** write completely */
                    return EXIT_SUCCESS;
                }
                else
                {
                    pcBufferIndex += iWriteLen;
                    continue;
                }
            }
            else
            {
                printf("tcp_iSendMsgTimeout send error\n");
                return EXIT_FAILURE;
            }
        }
        else if(0 == iOnce)
        {
            continue; // select timeout 
        }
        else
        {
            printf("tcp_iSendMsgTimeout select error\n");
            return EXIT_FAILURE;
        }
    }
    return TCP_TIMEOUT; 
}



#endif
```


<br />
<br />
<br />



### tcpunit_linux.cpp


```cpp
#include "tcpunit.h"


void tcp_SetNonblock(int iSocket)
{
    int opts;
    opts = fcntl(iSocket, F_GETFL);
    if(opts < 0) {
        LOG_PRINTF("vSetNonBlocking F_GETFL failed\n");
        exit(1);
    }

    opts = (opts | O_NONBLOCK);
    if(fcntl(iSocket, F_SETFL, opts) < 0) {
        LOG_PRINTF("vSetNonBlocking F_SETFL failed\n");
        exit(1);
    }
}

void tcp_Setblock(int iSocket)
{
    //TODO
}

void tcp_vCloseSocket(int & riSocket)
{
    if(riSocket != 0)
    {
        close(riSocket);
        riSocket = 0;
    }
}

int 
tcp_iConnect(int * piSocket, const char * pcAddr, unsigned short usPort, int iTimeout)
{
    //TODO
    return EXIT_SUCCESS;
}

int
tcp_iRecvMsg(int iSocket, char * pcBuf, int * piBufLen)
{
    if(iSocket <= 0 || NULL == pcBuf || NULL == piBufLen || (*piBufLen < 0)) {
        printf("tcp_iRecvMsg input error!\n");
        return EXIT_FAILURE;
    }

    struct timeval stTimeInterval;
    stTimeInterval.tv_sec = 0;
    stTimeInterval.tv_usec = 100 * 1000;
    fd_set stReadSet;
    FD_ZERO(&stReadSet);
    FD_SET(iSocket, &stReadSet);
    int iActiveNum = select(iSocket + 1, &stReadSet, NULL, NULL, &stTimeInterval);
    if(iActiveNum == 1 && FD_ISSET(iSocket, &stReadSet))
    {
        int iRecv = recv(iSocket, pcBuf , *piBufLen, 0);
        if(iRecv > 0)
        {
            *piBufLen = iRecv;
            return EXIT_SUCCESS;
        }
        else if(iRecv == 0)
        {
            return TCP_EOF;
        }
    }
    else if(iActiveNum == 0)
    {
        return TCP_TIMEOUT;
    }
    return EXIT_FAILURE;
}


int
tcp_iRecvMsgTimeout(int iSocket, char * pcBuf, int * piBufLen, int iTimeout)
{
    if(iSocket <= 0 || NULL == pcBuf || NULL == piBufLen || (*piBufLen <= 0)) {
        printf("tcp_iRecvMsgTimeout input error!\n");
        return EXIT_FAILURE;
    }  

    struct timeval stTimeInterval;
    struct timeval stTimeIntervalTemp;
    stTimeInterval.tv_sec = 0;
    stTimeInterval.tv_usec = 10 * 1000;

    int iOnce = 0;
    int iRecv = 0;
    char *pcIndex = pcBuf;

    unsigned int uiCurrentTime = iclock();
    unsigned int uiEndTime;
    if(iTimeout <= 0) uiEndTime = uiCurrentTime + 10 * 1000;
    else uiEndTime = uiCurrentTime + iTimeout * 1000;

    while ( (uiCurrentTime = iclock()) < uiEndTime )
    {
        stTimeIntervalTemp = stTimeInterval;
        fd_set stReadSet;
        FD_ZERO(&stReadSet);
        FD_SET(iSocket, &stReadSet);
        iOnce = select(iSocket + 1, &stReadSet , NULL, NULL, &stTimeIntervalTemp);
        if(1 == iOnce && FD_ISSET(iSocket, &stReadSet))
        {
            iOnce = recv(iSocket, pcIndex , *piBufLen - iRecv, 0);
            if(iOnce > 0)
            {
                iRecv += iOnce;
                if(iRecv >= *piBufLen)
                {
                    return EXIT_SUCCESS;
                }
                else
                {
                    pcIndex += iOnce;
                    continue;
                }
            }
            else if(iOnce == 0)
            {
                return TCP_EOF;
            }
        }
        else if(iOnce == 0)
        {
            continue;
        }
        return EXIT_FAILURE;
    }

    *piBufLen = iRecv;
    return TCP_TIMEOUT;
}


int 
tcp_iSendMsg(int iSocket, const char * pcBuf, int * piBufLen)
{
    if(iSocket <= 0 || NULL == pcBuf || NULL == piBufLen || (*piBufLen <= 0)) {
        printf("tcp_iSendMsg input error!\n");
        return EXIT_FAILURE;
    }

    int iSendLen = send(iSocket, pcBuf, *piBufLen, 0);
    if(iSendLen < 0) {
        printf("tcp_iSendMsg send error. return value = %d. \n",iSendLen);
        return EXIT_FAILURE;
    }

    *piBufLen = iSendLen;
    return EXIT_SUCCESS;
}

int 
tcp_iSendAllMsg(int iSocket, const char * pcBuf, int iBufLen)
{
    if(iSocket <= 0 || NULL == pcBuf || 0 == iBufLen) {
        printf("tcp_iSendAllMsg input error!\n");
        return EXIT_FAILURE;
    }

    int iSendLen = 0;
    while(iSendLen < iBufLen)
    {
        int iSendLenTmp = send(iSocket, pcBuf + iSendLen, iBufLen - iSendLen, 0);
        if(iSendLenTmp < 0) {
            printf("tcp_iSendAllMsg send error. return value = %d. \n",iSendLenTmp);
            return EXIT_FAILURE;
        }

        iSendLen += iSendLenTmp;
    }

    return EXIT_SUCCESS;
}


int 
tcp_iSendMsgTimeout(int iSocket, const char * pcBuf, int iSize, int iTimeout)
{
    if(iSocket <= 0 || NULL == pcBuf || iSize <= 0) {
        printf("tcp_iSendMsgTimeout input error!\n");
        return EXIT_FAILURE;
    }

    int iTotalWriteLen = 0;
    char * pcBufferIndex = (char *)pcBuf;

    unsigned int uiCurrentTime = iclock();
    unsigned int uiEndTime;
    if(iTimeout <= 0) uiEndTime = uiCurrentTime + 100 * 1000;
    else uiEndTime = uiCurrentTime + iTimeout * 1000;

    while((uiCurrentTime = iclock()) < uiEndTime)
    {
        struct timeval stTimeInterval;
        stTimeInterval.tv_sec = 0;
        stTimeInterval.tv_usec = 10 * 1000;

        fd_set stWriteSet;
        FD_ZERO(&stWriteSet);
        FD_SET(iSocket, &stWriteSet);
        int iOnce = select(iSocket + 1, NULL , &stWriteSet, NULL, &stTimeInterval);
        if(1 == iOnce && FD_ISSET(iSocket, &stWriteSet))
        {
            int iWriteLen = send(iSocket, pcBufferIndex, iSize - iTotalWriteLen, 0);
            if(iWriteLen > 0)
            {
                iTotalWriteLen += iWriteLen;
                if(iTotalWriteLen >= iSize)
                {
                    /** write completely */
                    return EXIT_SUCCESS;
                }
                else
                {
                    pcBufferIndex += iWriteLen;
                    continue;
                }
            }
            else
            {
                printf("tcp_iSendMsgTimeout send error\n");
                return EXIT_FAILURE;
            }
        }
        else if(0 == iOnce)
        {
            continue; // select timeout 
        }
        else
        {
            printf("tcp_iSendMsgTimeout select error\n");
            return EXIT_FAILURE;
        }
    }
    return TCP_TIMEOUT; 
}
```


> 下面的 iclock() 代码来自 kcp, 如果我没有记错的话。



### utility.h

```cpp
#ifndef CPUB_UTILITY_H
#define CPUB_UTILITY_H

/* get clock in millisecond 64 */
long long  iclock64(void);

/* get clock in millisecond 32 */
unsigned int iclock();

/* sleep in millisecond */
void isleep(unsigned long millisecond);

#endif
```


### utility.cpp

```cpp
#include "utility.h"

#include <stdio.h>
#include <stdlib.h>
#include <time.h>
#include <ctype.h>
#include <string.h>


#if defined(WIN32) || defined(_WIN32) || defined(WIN64) || defined(_WIN64)
#include <windows.h>
#elif !defined(__unix)
#define __unix
#endif


#ifdef __unix
#include <unistd.h>
#include <sys/time.h>
#include <sys/wait.h>
#include <sys/types.h>
#endif


/* get system time */
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
    static long long  freq = 1;
    long long  qpc;
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

long long  iclock64(void)
{
    long s, u;
    long long  value;
    itimeofday(&s, &u);
    value = ((long long )s) * 1000 + (u / 1000);
    return value;
}

unsigned int iclock()
{
    return (unsigned int)(iclock64() & 0xfffffffful);
}

void isleep(unsigned long millisecond)
{
    #ifdef __unix   /* usleep( time * 1000 ); */
    /* struct timespec ts; 
    ts.tv_sec = (time_t)(millisecond / 1000);
    ts.tv_nsec = (long)((millisecond % 1000) * 1000000);
    nanosleep(&ts, NULL);*/
    usleep((millisecond << 10) - (millisecond << 4) - (millisecond << 3));
    #elif defined(_WIN32)
    Sleep(millisecond);
    #endif
}
```



