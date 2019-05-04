---
layout: post
title: "[Art][面试题] 2015华为校招机试"
date: 2017-03-06
author: mdgsf
comments: true
categories: Art
tags: [C,String,Permutation]
description:
published: true #default true
---

[http://blog.csdn.net/Hackbuteer1/article/details/39253767](http://blog.csdn.net/Hackbuteer1/article/details/39253767)

第一题(60分)：
按要求分解字符串，输入两个数M，N；
M代表输入的M串字符串，
N代表输出的每串字符串的位数，不够补0。
例如：输入2,8， “abc” ，“123456789”，则输出为“abc00000”,“12345678“,”90000000”

```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

void func(char * pcStr, int n)
{
    if(NULL == pcStr || n <= 0) {
        return ;
    }

    int iLen = strlen(pcStr);
    printf("##########%s %d\n", pcStr, iLen);

    int iCurCount = 0;
    for (int i = 0; i < iLen; i++)
    {
        printf("%c", pcStr[i]);
        iCurCount++;

        if(iCurCount == n)
        {
            iCurCount = 0;
            printf("\n");
        }
    }

    while (iCurCount < n)
    {
        printf("0");
        iCurCount++;
    }
    printf("\n");
}

int main()
{
    int m = 0;
    int n = 0;
    char acStr[1024] = {0};

    while (scanf("%d %d", &m, &n) != EOF)
    {
        for (int i = 0; i < m; i++)
        {
            scanf("%s", acStr);
            func(acStr, n);
        }
    }
    return 0;
}
```

> 第一题：拼音转数字
> 输入是一个只包含拼音的字符串，请输出对应的数字序列。转换关系如下：
> 描述：      拼音        yi  er  san  si  wu  liu  qi  ba  jiu
> 阿拉伯数字        1   2   3      4   5    6    7   8   9
> 输入字符只包含小写字母，所有字符都可以正好匹配
>
> 运行时间限制：无限制
> 内存限制：       无限制
> 输入：              一行字符串，长度小于1000
> 输出：              一行字符（数字）串
> 样例输入：       yiersansi
> 样例输出：       1234

```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

typedef struct _STrieNode
{
    char m_c;
    int m_iNumber;
    int m_iLen;
    bool m_bIsEnd;
    struct _STrieNode * m_apstChild[26];
    _STrieNode()
    {
        m_iLen = 0;
        m_bIsEnd = false;
        memset(m_apstChild, 0, sizeof(m_apstChild));
    }
}STrieNode;

class CTrie
{
public:
    CTrie()
    {
    }

    int m_iInsert(const char * pcStr, int iNumber)
    {
        if(NULL == pcStr) {
            return EXIT_FAILURE;
        }

        int iLen = strlen(pcStr);
        STrieNode * pstCurNode = &m_stRoot;
        for (int i = 0; i < iLen; i++)
        {
            if(pstCurNode->m_apstChild[ pcStr[i]-'a' ] == NULL)
            {
                STrieNode * pstNewNode = (STrieNode*)malloc(sizeof(STrieNode));
                if(NULL == pstNewNode) {
                    return EXIT_FAILURE;
                }

                memset(pstNewNode, 0, sizeof(STrieNode));

                pstNewNode->m_c = pcStr[i];
                pstNewNode->m_bIsEnd = (i==iLen-1) ? true : false;
                pstCurNode->m_apstChild[ pcStr[i]-'a' ] = pstNewNode;
            }

            pstCurNode = pstCurNode->m_apstChild[ pcStr[i]-'a' ];

            if(i == iLen-1)
            {
                pstCurNode->m_iLen = iLen;
                pstCurNode->m_iNumber = iNumber;
            }
        }

        return EXIT_SUCCESS;
    }

    int m_iFind(const char * pcStr, int * piNumber, int * piLen)
    {
        if(NULL == pcStr || NULL == piNumber || NULL == piLen) {
            return EXIT_FAILURE;
        }

        int iStrLen = strlen(pcStr);
        STrieNode * pstCurNode = &m_stRoot;
        for (int i = 0; i < iStrLen; i++)
        {
            pstCurNode = pstCurNode->m_apstChild[ pcStr[i] - 'a' ];
            if(pstCurNode == NULL)
            {
                return EXIT_FAILURE;
            }

            if(pstCurNode->m_bIsEnd)
            {
                *piNumber = pstCurNode->m_iNumber;
                *piLen = pstCurNode->m_iLen;
                return EXIT_SUCCESS;
            }
        }

        return EXIT_FAILURE;
    }

private:
    STrieNode m_stRoot;
};

CTrie g_oTrieTree;

void func(const char * pcStr)
{
    if(NULL == pcStr) {
        return ;
    }

    int iStrLen = strlen(pcStr);
    const char * pcIndex = pcStr;
    while (*pcIndex != '\0' && iStrLen > 0)
    {
        int iNumber = 0;
        int iLen = 0;
        int iRet = g_oTrieTree.m_iFind(pcIndex, &iNumber, &iLen);
        if(iRet != EXIT_SUCCESS) {
            printf("func m_iFind failed\n");
            return ;
        }
        printf("%d", iNumber);
        pcIndex += iLen;
        iStrLen -= iLen;
    }

    printf("\n");
}


int main()
{
    g_oTrieTree.m_iInsert("yi", 1);
    g_oTrieTree.m_iInsert("er", 2);
    g_oTrieTree.m_iInsert("san", 3);
    g_oTrieTree.m_iInsert("si", 4);

    g_oTrieTree.m_iInsert("wu", 5);
    g_oTrieTree.m_iInsert("liu", 6);
    g_oTrieTree.m_iInsert("qi", 7);
    g_oTrieTree.m_iInsert("ba", 8);
    g_oTrieTree.m_iInsert("jiu", 9);

    char acStr[1024] = {0};

    while (scanf("%s", acStr) != EOF)
    {
        func(acStr);
    }
    return 0;
}
```

> 第二题：去除重复字符并排序
> 运行时间限制：无限制
> 内容限制：       无限制
> 输入：              字符串
> 输出：              去除重复字符并排序的字符串
> 样例输入：       aabcdefff
> 样例输出：       abcdef

```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

void func(char * pcStr)
{
    if(NULL == pcStr) {
        return;
    }

    int iStrLen = strlen(pcStr);
    for (int i = 0; i < iStrLen; )
    {
        printf("%c", pcStr[i]);
        if(i == iStrLen - 1)
        {
            break;
        }

        int j;
        for (j = i+1; j < iStrLen; j++)
        {
            if(pcStr[i] != pcStr[j])
            {
                i = j;
                break;
            }
        }
        if(j == iStrLen)
        {
            break;
        }
    }
    printf("\n");
}

int main()
{
    char acStr[1024] = {0};

    while (scanf("%s", acStr) != EOF)
    {
        func(acStr);
    }
    return 0;
}
```

> 第三题：等式变换
> 输入一个正整数X，在下面的等式左边的数字之间添加+号或者-号，使得等式成立。
> 1 2 3 4 5 6 7 8 9 = X
> 比如：
> 12-34+5-67+89 = 5
> 1+23+4-5+6-7-8-9 = 5
> 请编写程序，统计满足输入整数的所有整数个数。
> 输入：       正整数，等式右边的数字
> 输出：       使该等式成立的个数
> 样例输入：5
> 样例输出：21

```cpp
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <iostream>
#include <stack>
using namespace std;

int iInfixToPostfix(const char * pcInfix, char * pcPostfix)
{
    if(NULL == pcInfix || NULL == pcPostfix) {
        return EXIT_FAILURE;
    }

    stack<char> myStack;
    int iInfixLen = strlen(pcInfix);
    for (int i = 0; i < iInfixLen; i++)
    {
        char c = pcInfix[i];
        if(c >= '0' && c <= '9')
        {
            *pcPostfix++ = c;
        }
        else
        {
            switch(c)
            {
            case '+':
            case '-':
                {
                    while (!myStack.empty())
                    {
                        *pcPostfix++ = myStack.top();
                        myStack.pop();
                    }
                    myStack.push(c);
                }
                break;
            case '?':
                {
                    while (!myStack.empty())
                    {
                        if(myStack.top() == '?')
                        {
                            *pcPostfix++ = myStack.top();
                            myStack.pop();
                        }
                        else
                        {
                            break;
                        }
                    }
                    myStack.push(c);
                }
                break;
            default:
                printf("unknown c = %d\n", c);
                break;
            }
        }
    }

    while (!myStack.empty())
    {
        *pcPostfix++ = myStack.top();
        myStack.pop();
    }

    return EXIT_SUCCESS;
}


int iCalcPostfix(const char * pcPostfix, int * piResult)
{
    if(NULL == pcPostfix) {
        return EXIT_FAILURE;
    }

    stack<int> myStack;
    int iLen = strlen(pcPostfix);

    for (int i = 0; i < iLen; i++)
    {
        char c = pcPostfix[i];
        if(c >= '0' && c <= '9')
        {
            myStack.push(c - '0');
        }
        else
        {
            if(myStack.size() < 2) {
                return EXIT_FAILURE;
            }

            int iNum2 = myStack.top();
            myStack.pop();

            int iNum1 = myStack.top();
            myStack.pop();

            switch(c)
            {
            case '+':
                myStack.push(iNum1 + iNum2);
                break;
            case '-':
                myStack.push(iNum1 - iNum2);
                break;
            case '?':
                myStack.push(iNum1*10 + iNum2);
                break;
            default:
                printf("unknown c = %c\n", c);
                break;
            }
        }
    }

    if(myStack.size() != 1)
    {
        return EXIT_FAILURE;
    }

    *piResult = myStack.top();

    return EXIT_SUCCESS;
}

int iCount = 0;
char aiOpts[20] = {0};

void func(int n, int iCurNum, int iOptIdx)
{
    if(iCurNum == 10)
    {
        char acStr[1024] = {0};
        acStr[0] = '1';
        int iStrIdx = 1;
        for (int i = 0; i < iOptIdx; i++)
        {
            acStr[iStrIdx++] = aiOpts[i];
            acStr[iStrIdx++] = i + 2 + '0';
        }

        char acPostfix[1024] = {0};
        iInfixToPostfix(acStr, acPostfix);

        int iRet = 0;
        iCalcPostfix(acPostfix, &iRet);

        if(iRet == 5)
        {
            iCount++;
        }

        return ;
    }

    aiOpts[iOptIdx] = '+';
    func(n, iCurNum+1, iOptIdx+1);

    aiOpts[iOptIdx] = '-';
    func(n, iCurNum+1, iOptIdx+1);

    aiOpts[iOptIdx] = '?';
    func(n, iCurNum+1, iOptIdx+1);
}

int main()
{
    int n;
    while (scanf("%d", &n) != EOF)
    {
        iCount = 0;
        func(n, 2, 0);
        printf("iCount = %d\n", iCount);
    }
    return 0;
}
```
