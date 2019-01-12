---
layout: post
title: "[排列组合] N 皇后问题"
date: 2017-03-09
author: mdgsf
comments: true
categories: Art
tags: c 
description:
published: true #default true
---



### 用二维数组

```cpp
#include <unistd.h>
#include <sys/types.h>
#include <sys/time.h>
#include <sys/resource.h>
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <iostream>
#include <stack>
using namespace std;

#define N 8

int aiBoard[N][N] = {0};

int iCount = 0;

void vShowBoard()
{
    for (int i = 0; i < N; i++)
    {
        for (int j = 0; j < N; j++)
        {
            printf("%d ", aiBoard[i][j]);
        }
        printf("\n");
    }
    printf("\n");
}

bool bIsValid(int iRow, int iCol)
{
    for (int i = 0; i < iRow; i++)
    {
        if( (aiBoard[i][iCol] == 1) ||
            ( ( iCol - (iRow - i) >= 0) && (aiBoard[i][ iCol - (iRow - i) ] == 1) ) ||
            ( ( iCol + (iRow - i) < N ) && (aiBoard[i][ iCol + (iRow - i) ] == 1) )
          )
        {
            return false;
        }
    }
    return true;
}

void vFunc(int iRow)
{
    if(iRow >= N)
    {
        vShowBoard();
        iCount++;
    }
    else
    {
        for (int iCol = 0; iCol < N; iCol++)
        {
            if(bIsValid(iRow, iCol))
            {
                aiBoard[iRow][iCol] = 1;
                vFunc(iRow+1);
                aiBoard[iRow][iCol] = 0;
            }
        }
    }
}

int main()
{
    vFunc(0);
    printf("iCount = %d\n", iCount);
    return 0;
}
```


### 用一维数组

```cpp
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

#define N 8

//key: iRow,  value: iCol
int aiBoard[N] = {0};

int iCount = 0;

void vShowBoard()
{
    for (int i = 0; i < N; i++)
    {
        int j;
        for (j = 0; j < aiBoard[i]; j++) {
            printf("X ");
        }
        printf("Q ");
        for (j = aiBoard[i]+1; j < N; j++) {
            printf("X ");
        }

        printf("\n");
    }
    printf("\n");
}

bool bIsValid(int iRow, int iCol)
{
    for (int i = 0; i < iRow; i++)
    {
        if( (aiBoard[i] == iCol) || (abs(iRow - i) == abs(iCol - aiBoard[i])) )
        {
            return false;
        }
    }
    return true;
}

void vFunc(int iRow)
{
    if(iRow >= N)
    {
        vShowBoard();
        iCount++;
    }
    else
    {
        for (int iCol = 0; iCol < N; iCol++)
        {
            if(bIsValid(iRow, iCol))
            {
                aiBoard[iRow] = iCol;
                vFunc(iRow+1);
            }
        }
    }
}

int main()
{
    vFunc(0);
    printf("iCount = %d\n", iCount);
    return 0;
}
```


