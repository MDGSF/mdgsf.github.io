---
layout: post
title:  "[C/C++] 线程加锁 例子"
date:   2016-05-30
comments: true
categories: C/C++
tags: [C]
description:
published: true
---

```cpp
    apr_thread_mutex_lock(m_pstMutex);
    apr_int32_t iMaxTryTime = m_oLBServerVector.size();
    apr_thread_mutex_unlock(m_pstMutex);
    while ( iMaxTryTime-- > 0 )
    {
        char acServerIPAddr[MAX_IPADDRESS_LENGTH] = {0};
        apr_uint32_t uiServerPort;

        apr_thread_mutex_lock(m_pstMutex);
        vector<SServerAddr>::iterator oLBIter = m_oLBServerVector.begin();
        strcpy(acServerIPAddr, oLBIter->m_acServerIPAddr);
        uiServerPort = oLBIter->m_uiServerPort;
        apr_thread_mutex_unlock(m_pstMutex);

        iRet = m_iQueryFromServerTCP(
                acServerIPAddr,
                uiServerPort,
                acSendMsgBuf,
                usSendMsgLen,
                acRecvMsgBuf,
                &usRecvMsgLen,
                &ucMsgType);
        if(APR_SUCCESS == iRet) {
            break;
        }

        apr_thread_mutex_lock(m_pstMutex);
        SServerAddr stServerAddr = *oLBIter;
        m_oLBServerVector.erase(oLBIter);
        m_oLBServerVector.push_back(stServerAddr);
        apr_thread_mutex_unlock(m_pstMutex);
    }
    if(iRet != APR_SUCCESS) {
        return iRet;
    }
```

(note:一个变量作为一个类的成员变量时，是多个线程共享的。作为临时变量，则是每个线程一份。)

这段代码的加锁有问题。
问题：当两个线程都执行到 erase 的时候，vector被移动了两次。

解决方法一：把 m_iQueryFromServerTCP（）函数一起加锁，但是这样的话，锁太大，不建议采用。

解决方法二：在 erase 之前，先判断下当前的iter指向的是不是和之前取出来的一样。
        一样的话就移动，
        不一样的话就不移动。

解决方法三：把 iter 作为一个成员变量，这样就所有的线程都共享，
        在构造函数中初始化 iter = m_oLBServerVector.begin();
        然后每次都取出 iter 当前指向的值，
        失败的时候就把 iter 向后移动一格。
        （这种方法相当于是一个ring，所有线程共享一个指针。）

解决方法四：用一个ring，每个线程都有一个自己的移动指针。

解决方法三 的代码如下：

```cpp
    apr_thread_mutex_lock(m_pstMutex);
    apr_int32_t iMaxTryTime = m_oLBServerVector.size();
    apr_thread_mutex_unlock(m_pstMutex);
    while ( iMaxTryTime-- > 0 )
    {
        char acServerIPAddr[MAX_IPADDRESS_LENGTH] = {0};
        apr_uint32_t uiServerPort;

        apr_thread_mutex_lock(m_pstMutex);
        strcpy(acServerIPAddr, m_oLBIter->m_acServerIPAddr);
        uiServerPort = m_oLBIter->m_uiServerPort;
        apr_thread_mutex_unlock(m_pstMutex);

        iRet = m_iQueryFromServerTCP(
                acServerIPAddr,
                uiServerPort,
                acSendMsgBuf,
                usSendMsgLen,
                acRecvMsgBuf,
                &usRecvMsgLen,
                &ucMsgType);
        if(APR_SUCCESS == iRet) {
            break;
        }

        apr_thread_mutex_lock(m_pstMutex);
        if(strcmp(acServerIPAddr, m_oLBIter->m_acServerIPAddr) == 0 &&
           uiServerPort == m_oLBIter->m_uiServerPort)
        {
            m_oLBIter++;
            if(m_oLBIter == m_oLBServerVector.end()) {
                m_oLBIter = m_oLBServerVector.begin();
            }
        }
        apr_thread_mutex_unlock(m_pstMutex);
    }
    if(iRet != APR_SUCCESS) {
        return iRet;
    }
```
