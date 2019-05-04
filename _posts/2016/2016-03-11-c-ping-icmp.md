---
layout: post
title:  "[Windows] Ping icmp"
date:   2016-03-11
comments: true
categories: Windows
tags: [C,Ping,Icmp]
description:
published: true
---

### 参考链接：

[http://blog.csdn.net/zpxili/article/details/11542041](http://blog.csdn.net/zpxili/article/details/11542041)

[http://blog.csdn.net/cbuttonst/article/details/7610801](http://blog.csdn.net/cbuttonst/article/details/7610801)

上面两位兄弟的是发一个icmp包，就收一次。在我的电脑上运行的时候，

估计是我的网络中有回环，结果每次收到的都是刚刚发出去的icmp包，

用wireshark抓包又能够抓到正确的reply包。调试了好久，修改如下。

### CPing.h

```cpp
#ifndef CPING_HJ
#define CPING_HJ
#include <WinSock2.h>
#include <string>
typedef struct _SICMPHDR        //icmp
{
    unsigned char    icmp_type;
    unsigned char    icmp_code;
    unsigned short    icmp_checksum;
    unsigned short    icmp_id;
    unsigned short    icmp_sequence;
    unsigned long    icmp_timestamp;
} ICMPHDR, *PICMPHDR;
typedef struct _SIPHDR        //ip
{
    UCHAR    ip_hVerLen;
    UCHAR    ip_TOS;
    USHORT    ip_Length;
    USHORT    ip_ID;
    USHORT    ip_Flags;
    UCHAR    ip_TTL;
    UCHAR    ip_Protacol;
    USHORT    ip_Checksum;
    ULONG    ip_Source;
    ULONG    ip_Destination;
} IPHDR;
class CPing
{
public:
    CPing();
    ~CPing();
    void m_vPing();
private:
    bool m_bSendData(char* pcBuf,int nBufLen,sockaddr_in* pstAddr);
    bool m_bRecvData(char* pcBuf,int iBufLen,sockaddr_in* pstRecvAddr,int &riRecvLen);
    void m_vInitICMP(PICMPHDR pstICMPHDR,int nSequence);
    u_short m_usCheckSum(unsigned short *pusBuf,int iLen);
    SOCKET m_iSocket;
};
#endif
```

### CPing.cpp

```cpp
#include "CPing.h"
#include <stdio.h>
#define DATA_SIZE 32
#define RECV_SIZE 1024
const int SEND_PACKAGE_SIZE = 30;
const int MAX_TIME = 3000; //ms
CPing::CPing()
{
    WSADATA wsaData;
    WORD wVersion;
    wVersion = MAKEWORD(2,2);
    int nRet = WSAStartup(wVersion,&wsaData);
    if( nRet != 0 )
    {
        printf("WSAStartup failed with error: %d\n", nRet);
        return;
    }
    if( LOBYTE(wsaData.wVersion) != 2 || HIBYTE(wsaData.wVersion) != 2 )
    {
        printf("Could not find a usable version of Winsock.dll\n");
        WSACleanup();
        return;
    }
    m_iSocket = socket(AF_INET, SOCK_RAW, IPPROTO_ICMP);
    if( m_iSocket == INVALID_SOCKET )
    {
        printf("Socket err\n");
        WSACleanup();
        return;
    }
}
CPing::~CPing()
{
    if( INVALID_SOCKET != m_iSocket )
    {
        closesocket(m_iSocket);
    }
    WSACleanup();
}
void
CPing::m_vPing()
{
    char acName[] = "www.vidagrid.com";
    hostent *pstHost = gethostbyname(acName);
    if( pstHost == NULL )
    {
        printf("CPing::m_vPing() gethostbyname err\n");
        return;
    }
    sockaddr_in stDesAddr;
    stDesAddr.sin_family = AF_INET;
    stDesAddr.sin_port = htons(0);
    stDesAddr.sin_addr = *((struct in_addr *)(pstHost->h_addr));
    //stDesAddr.sin_addr.s_addr = inet_addr("172.17.92.110");
    char acIPAddr[23] = {0};
    strcpy(acIPAddr, inet_ntoa(stDesAddr.sin_addr));
    printf("CPing::m_vPing() acIPAddr: %s\n", acIPAddr);
    int iSendBufLen = sizeof(ICMPHDR) + DATA_SIZE;
    char* pcIcmp = new char[iSendBufLen];
    memset(pcIcmp, 0, iSendBufLen);
    PICMPHDR pstIcmp = (PICMPHDR)pcIcmp;
    int iSequence = 0;
    int iCount = SEND_PACKAGE_SIZE;
    while ( iCount-- > 0 )
    {
        m_vInitICMP(pstIcmp, iSequence++);
        pstIcmp->icmp_checksum = m_usCheckSum((unsigned short*)pstIcmp,sizeof(ICMPHDR) + DATA_SIZE); //校验值
        m_bSendData((char*)pstIcmp, iSendBufLen, &stDesAddr);
    }
    int iMinimum = 0;
    int iMaximum = 0;
    int iSumTime = 0;
    iCount = 0;
    DWORD dwStartTime = GetTickCount();
    printf("\nPinging %s [%s] with 32 bytes of data:\n\n", acName, acIPAddr);
    while( (GetTickCount()-dwStartTime) <= MAX_TIME)
    {
        sockaddr_in stRcvAddr;
        char acBuf[RECV_SIZE] = {0};
        int iRecvLen = 0;
        m_bRecvData(acBuf, sizeof(acBuf), &stRcvAddr, iRecvLen);
        int iHeadLen = sizeof(IPHDR) + sizeof(ICMPHDR) + DATA_SIZE;
        if( iRecvLen < iHeadLen )
        {
            printf("CPing::m_vPing() tool few data~\n\n");
            continue;
        }
        IPHDR *ipHead = (IPHDR *)acBuf;
        PICMPHDR icmpRecv = (PICMPHDR) (acBuf + sizeof(IPHDR) );
        if( icmpRecv->icmp_type != 0 )
        {
            printf("CPing::m_vPing() Icmp Type err~, 0x%x\n\n", icmpRecv->icmp_type);
            continue;
        }
        if( icmpRecv->icmp_id != GetCurrentProcessId() )
        {
            printf("CPing::m_vPing() Icmp ID err~\n\n");
            continue;
        }
        int iCurUsedTime = GetTickCount() - icmpRecv->icmp_timestamp;
        printf("Reply from %s: bytes=%d time=%ums TTL=%d\n",
            inet_ntoa(stRcvAddr.sin_addr),
            DATA_SIZE,
            iCurUsedTime,
            ipHead->ip_TTL);
        iSumTime += iCurUsedTime;
        if(iCount != 0)
        {
            if(iCurUsedTime<iMinimum) iMinimum = iCurUsedTime;
            else if(iCurUsedTime>iMaximum) iMaximum = iCurUsedTime;
        }
        else
        {
            iMinimum = iCurUsedTime;
            iMaximum = iCurUsedTime;
        }
        iCount++;
    }
    //show statistics
    printf("\nPing statistics for %s:\n", acIPAddr);
    printf("\tPackets: Sent = %d, Received = %d, Lost = %d (%.2lf%% loss),\n",
        SEND_PACKAGE_SIZE, iCount, SEND_PACKAGE_SIZE-iCount, ((double)(SEND_PACKAGE_SIZE-iCount))/SEND_PACKAGE_SIZE*100 );
    printf("Approximate round trip times in milli-seconds:\n");
    printf("\tMinimum = %dms, Maximum = %dms, Average = %dms\n",
        iMinimum, iMaximum, iSumTime/iCount);
    delete pcIcmp;
    getchar();
}
bool
CPing::m_bSendData(char* pcBuf,int iBufLen,sockaddr_in* pstAddr)
{
    printf("CPing::m_bSendData() \n");
    if( pstAddr == NULL )
    {
        printf("CPing::m_bSendData() pstAddr == NULL\n");
        return false;
    }
    int iTimeout = 1000;
    int iRet = setsockopt(m_iSocket, SOL_SOCKET, SO_SNDTIMEO, (char*)&iTimeout, sizeof(int));
    if( iRet == SOCKET_ERROR )
    {
        printf("CPing::m_bSendData() setsockopt SO_SNDTIMEO err\n");
        return false;
    }
    iRet = sendto(m_iSocket, pcBuf, iBufLen, 0, (sockaddr*)pstAddr, sizeof(sockaddr));
    if( iRet == SOCKET_ERROR )
    {
        if (WSAETIMEDOUT == WSAGetLastError())
        {
            printf("CPing::m_bSendData() timeout err\n");
            return false;
        }
        else
        {
            printf("CPing::m_bSendData() sendto err\n");
            return false;
        }
    }
    return true;
}
bool
CPing::m_bRecvData(char* pcBuf,int iBufLen,sockaddr_in* pstRecvAddr,int &riRecvLen)
{
    printf("CPing::m_bRecvData() \n");
    if( INVALID_SOCKET == m_iSocket )
    {
        printf("CPing::m_bRecvData() INVALID_SOCKET\n");
        return false;
    }
    int iTimeout = 1000;
    int iRet = setsockopt(m_iSocket, SOL_SOCKET, SO_RCVTIMEO , (char*)&iTimeout, sizeof(int));
    if( SOCKET_ERROR == iRet )
    {
        printf("CPing::m_bRecvData() setsockopt SO_RCVTIMEO error\n");
        return false;
    }
    int nAddrLen = sizeof(sockaddr);
    iRet = recvfrom(m_iSocket, pcBuf, iBufLen, 0, (sockaddr*)pstRecvAddr, &nAddrLen);
    if( SOCKET_ERROR == iRet )
    {
        if (WSAETIMEDOUT == WSAGetLastError())
        {
            printf("CPing::m_bRecvData() timeout err\n");
            return false;
        }
        else
        {
            printf("CPing::m_bRecvData() recvfrom err\n");
            return false;
        }
    }
    riRecvLen = iRet;
    return true;
}
void
CPing::m_vInitICMP(PICMPHDR pstICMPHDR,int iSequence)
{
    if( pstICMPHDR == NULL )
        return;
    pstICMPHDR->icmp_type = 8; //request
    pstICMPHDR->icmp_code = 0; //icmp request
    pstICMPHDR->icmp_sequence = iSequence;
    pstICMPHDR->icmp_id = (unsigned short)GetCurrentProcessId();
    pstICMPHDR->icmp_timestamp = GetTickCount();
    pstICMPHDR->icmp_checksum = 0; //校验值
}
unsigned short
CPing::m_usCheckSum(unsigned short *pusBuf,int iLen)
{
    USHORT cksum=0;
    while(iLen>1)
    {
        cksum+=*pusBuf++;
        iLen-=sizeof(USHORT);
    }
    if(iLen)
    {
        cksum+=*pusBuf++;
    }
    cksum=(cksum>>16)+(cksum&0xffff);
    cksum+=(cksum>>16);
    return (USHORT)(~cksum);
}
```

//main.cpp

```cpp
#include "CPing.h"
#include <stdio.h>
#include <winsock2.h>
#include <iostream>
#include <windows.h>
using namespace std;
#pragma comment(lib, "ws2_32.lib")
int main()
{
    CPing oPing;
    oPing.m_vPing();
    return 0;
}
```

当 icmp_type==8 时，这是一个请求包（ECHO包）。

当 icmp_type==0 时，这是一个响应消息报（ECHO REPLY包），就是对请求包的回应。
