---
layout: post
title:  "[C/C++] GetAbsolutePath"
date:   2016-09-10
comments: true
categories: C/C++
tags: [C]
description:
published: true
---

```
问题描述
　　在操作系统中，数据通常以文件的形式存储在文件系统中。文件系统一般采用层次化的组织形式，由目录（或者文件夹）和文件构成，形成一棵树的形状。文件有内容，用于存储数据。目录是容器，可包含文件或其他目录。同一个目录下的所有文件和目录的名字各不相同，不同目录下可以有名字相同的文件或目录。
　　为了指定文件系统中的某个文件，需要用路径来定位。在类 Unix 系统（Linux、Max OS X、FreeBSD等）中，路径由若干部分构成，每个部分是一个目录或者文件的名字，相邻两个部分之间用 / 符号分隔。
　　有一个特殊的目录被称为根目录，是整个文件系统形成的这棵树的根节点，用一个单独的 / 符号表示。在操作系统中，有当前目录的概念，表示用户目前正在工作的目录。根据出发点可以把路径分为两类：
　　Ÿ 绝对路径：以 / 符号开头，表示从根目录开始构建的路径。
　　Ÿ 相对路径：不以 / 符号开头，表示从当前目录开始构建的路径。

　　例如，有一个文件系统的结构如下图所示。在这个文件系统中，有根目录 / 和其他普通目录 d1、d2、d3、d4，以及文件 f1、f2、f3、f1、f4。其中，两个 f1 是同名文件，但在不同的目录下。

　　对于 d4 目录下的 f1 文件，可以用绝对路径 /d2/d4/f1 来指定。如果当前目录是 /d2/d3，这个文件也可以用相对路径 ../d4/f1 来指定，这里 .. 表示上一级目录（注意，根目录的上一级目录是它本身）。还有 . 表示本目录，例如 /d1/./f1 指定的就是 /d1/f1。注意，如果有多个连续的 / 出现，其效果等同于一个 /，例如 /d1///f1 指定的也是 /d1/f1。
　　本题会给出一些路径，要求对于每个路径，给出正规化以后的形式。一个路径经过正规化操作后，其指定的文件不变，但是会变成一个不包含 . 和 .. 的绝对路径，且不包含连续多个 / 符号。如果一个路径以 / 结尾，那么它代表的一定是一个目录，正规化操作要去掉结尾的 /。若这个路径代表根目录，则正规化操作的结果是 /。若路径为空字符串，则正规化操作的结果是当前目录。
输入格式
　　第一行包含一个整数 P，表示需要进行正规化操作的路径个数。
　　第二行包含一个字符串，表示当前目录。
　　以下 P 行，每行包含一个字符串，表示需要进行正规化操作的路径。
输出格式
　　共 P 行，每行一个字符串，表示经过正规化操作后的路径，顺序与输入对应。
样例输入
7
/d2/d3
/d2/d4/f1
../d4/f1
/d1/./f1
/d1///f1
/d1/
///
/d1/../../d2
样例输出
/d2/d4/f1
/d2/d4/f1
/d1/f1
/d1/f1
/d1
/
/d2
评测用例规模与约定
　　1 ≤ P ≤ 10。
　　文件和目录的名字只包含大小写字母、数字和小数点 .、减号 - 以及下划线 _。
　　不会有文件或目录的名字是 . 或 .. ，它们具有题目描述中给出的特殊含义。
　　输入的所有路径每个长度不超过 1000 个字符。
　　输入的当前目录保证是一个经过正规化操作后的路径。
　　对于前 30% 的测试用例，需要正规化的路径的组成部分不包含 . 和 .. 。
　　对于前 60% 的测试用例，需要正规化的路径都是绝对路径。
```


```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define PATHLEN 1024


/***    log    ***/
//#define DEBUG
#ifdef DEBUG
#define LOG(format, ...) \
    printf("<"__FILE__">(%05d): "format"", __LINE__, ##__VA_ARGS__)
#else
#define LOG(format, ...) 
#endif
/***    log end    ***/


/***    list    ***/
typedef struct _SNode {
    struct _SNode * prev;
    struct _SNode * next;
    char m_acDir[PATHLEN];
}SNode;

typedef struct _SList {
    struct _SNode * head;
    struct _SNode * tail;
}SList;

SNode * list_node_init(const char * pcDir)
{
    SNode * pNode = (SNode*)malloc(sizeof(SNode));
    pNode->prev = NULL;
    pNode->next = NULL;
    if(NULL == pcDir)
    {
        memset(pNode->m_acDir, 0, sizeof(pNode->m_acDir));
    }
    else
    {
        strcpy(pNode->m_acDir, pcDir);
    }
    return pNode;
}

SList * list_init()
{
    SList * pList = (SList*)malloc(sizeof(SList));
    SNode * pHead = list_node_init(NULL);
    pList->head = pHead;
    pList->tail = pHead;
    return pList;
}

void list_insert_one_node(SNode * pNewNode, SNode * pPreNode, SNode * pCurNode)
{
    pNewNode->prev = pPreNode;
    pNewNode->next = pCurNode;

    pPreNode->next = pNewNode;

    if(pCurNode != NULL)
    {
        pCurNode->prev = pNewNode;
    }
}

void list_delete_one_node(SNode * pNode)
{
    if(NULL == pNode) {
        return ;
    }

    SNode * pPreNode = pNode->prev;
    SNode * pNextNode = pNode->next;

    pPreNode->next = pNextNode;

    if(pNextNode != NULL) {
        pNextNode->prev = pPreNode;
    }

    free(pNode);
}

void list_insert_node_tail(SList * pList, SNode * pNode)
{
    list_insert_one_node(pNode, pList->tail, NULL);
    pList->tail = pNode;
}

void list_insert_tail(SList * pList, const char * pcData)
{
    SNode * pNode = list_node_init(pcData);
    list_insert_node_tail(pList, pNode);
}

SList * list_join(SList * first, SList * second)
{
    SList * pNewList = list_init();
    pNewList->tail->next = first->head->next;
    first->head->next->prev = pNewList->tail;

    first->tail->next = second->head->next;
    second->head->next->prev = first->tail;

    first->head->next = NULL;
    first->tail = first->head;
    second->head->next = NULL;
    second->tail = second->head;
    return pNewList;
}

void list_destroy(SList * pList)
{
    if(NULL == pList) {
        return ;
    }

    SNode * pCur = pList->head->next;
    while (pCur != NULL)
    {
        SNode * pNext = pCur->next;
        free(pCur);
        pCur = pNext;
    }
    free(pList);
}

void list_print(SList * pList)
{
#ifdef DEBUG
    SNode * pCur = pList->head->next;
    while (pCur != NULL)
    {
        printf("%s ", pCur->m_acDir);
        pCur = pCur->next;
    }
    printf("\n");
#endif
}
/***    list end    ***/


SList * pstChangePathToList(const char * pcPath)
{
    SList * pList = list_init();

    char acPath[PATHLEN] = {0};
    strcpy(acPath, pcPath);
    char * pStart = acPath;

    if(pStart[0] == '/') //absolute path
    {
        list_insert_tail(pList, "/");
        pStart++;
    }

    while (*pStart != '\0')
    {
        char * pEnd = strchr(pStart, '/');
        if(NULL == pEnd) //last one dir
        {
            list_insert_tail(pList, pStart);
            break;
        }
        else
        {
            if( pEnd == pStart ) //serial '/'
            {
                pStart++;
            }
            else
            {
                *pEnd = '\0';
                list_insert_tail(pList, pStart);
                pStart = pEnd + 1;
            }
        }
    }

    list_print(pList);
    return pList;
}

void vTrimAbsolutePath(SList * pList, char * pcPath)
{
    SNode * pCur = pList->head->next->next;
    if(NULL == pCur)
    {
        strcpy(pcPath, "/");
        return ;
    }

    //delete '.' and '..'
    while (pCur != NULL)
    {
        if( strcmp(".", pCur->m_acDir) == 0 )
        {
            SNode * pNext = pCur->next;
            list_delete_one_node(pCur);
            pCur = pNext;
        }
        else if( strcmp("..", pCur->m_acDir) == 0 )
        {
            SNode * pNext = pCur->next;
            list_delete_one_node(pCur);
            pCur = pNext;

            if(pCur != NULL && pCur->prev != pList->head->next) { //if prev is root '/'
                list_delete_one_node(pCur->prev);
            }
        }
        else
        {
            pCur = pCur->next;
        }
    }

    pCur = pList->head->next->next;
    if(NULL == pCur)
    {
        strcpy(pcPath, "/");
        return ;
    }

    while (pCur != NULL)
    {
        strcat(pcPath, "/");
        strcat(pcPath, pCur->m_acDir);
        pCur = pCur->next;
    }
}

void vTrimRelativePath(SList * pCurList, SList * pSrcList, char * pcPath)
{
    SList * pList = list_join(pCurList, pSrcList);
    vTrimAbsolutePath(pList, pcPath);
    list_destroy(pList);
}

void vGetAbsolutePath(const char * pcCurPath, const char * pcSrc, char * pcDst)
{
    if(NULL == pcCurPath || NULL == pcSrc || NULL == pcDst) {
        return ;
    }
    LOG("pcCurPath: %s\n", pcCurPath);
    LOG("pcSrc: %s\n", pcSrc);

    SList * pCurList = pstChangePathToList(pcCurPath);
    SList * pSrcList = pstChangePathToList(pcSrc);

    if(pcSrc[0] == '/') //absolute path
    {
        vTrimAbsolutePath(pSrcList, pcDst);
    }
    else //relative path
    {
        vTrimRelativePath(pCurList, pSrcList, pcDst);
    }

    list_destroy(pCurList);
    list_destroy(pSrcList);
}

int main()
{
    int n = 0;
    char acCurPath[PATHLEN] = {0};

    scanf("%d", &n);
    scanf("%s", acCurPath);

    for (int i = 0; i < n; i++)
    {
        char acSrcPath[PATHLEN] = {0};
        char acDstPath[PATHLEN] = {0};

        scanf("%s", acSrcPath);
        vGetAbsolutePath(acCurPath, acSrcPath, acDstPath);
        LOG("acDstPath: %s\n\n", acDstPath);
        printf("%s\n", acDstPath);
    }
    return 0;
}
```
