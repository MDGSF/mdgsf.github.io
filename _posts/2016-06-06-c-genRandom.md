---
layout: post
title:  "[C/C++] Generate random string"
date:   2016-06-06
comments: true
categories: C/C++
tags: [Rand]
description:
published: true
---



```cpp
#include <iostream>
#include <stdio.h>
#include <stdlib.h>
#include <ctime>
#include <string.h>
#include "unistd.h"

using namespace std;

void vGenRandom(char * str, const int iLen);
void vGenAlphaCapital(char * str, const int iLen);
void vGenAlphaLower(char * str, const int iLen);
void vGenNumber(char * str, const int iLen);
void vHelp();

#define ALPHA_NUM 26
#define NUMBER_NUM 10
#define CHARACTER_NUM 32

char g_acAlphaCapital[ALPHA_NUM+1] = "ABCDEFGHIJKLMNOPQRSTUVWXYZ";
char g_acAlphaLower[ALPHA_NUM+1] = "abcdefghijklmnopqrstuvwxyz";
char g_acNumber[NUMBER_NUM+1] = "0123456789";
char g_acCharacter[CHARACTER_NUM+1]= "!\"#$%&'()*+,-./:;<=>?@[\\]^_`{|}~";


int main(int argc, char *argv[])
{
    srand(time(NULL));
    int iRet = 0;
    int iType = 0; //default type
    int iLength = 10; //default length
    int iNumber = 1; //default number

    while ((iRet = getopt(argc, argv, "t:l:n:h")) != -1)
    {
        switch(iRet)
        {
        case 't':
            iType = ::atoi(optarg);
            break;
        case 'l':
            iLength = ::atoi(optarg);
            break;
        case 'n':
            iNumber = ::atoi(optarg);
            break;
        case 'h':
            {
                vHelp();
                return 0;
            }
        default:
            printf("Unknown parameters\n");
            break;
        }
    }


    switch(iType)
    {
    case 0:
        {
            for (int i = 0; i < iNumber; i++)
            {
                char acRandom[BUFSIZ] = {0};
                vGenRandom(acRandom, iLength);
                printf("%s ", acRandom);
            }
        }
        break;
    case 1:
        {
            for (int i = 0; i < iNumber; i++)
            {
                char acRandom[BUFSIZ] = {0};
                vGenNumber(acRandom, iLength);
                printf("%s ", acRandom);
            }
        }
        break;
    case 2:
        {
            for (int i = 0; i < iNumber; i++)
            {
                char acRandom[BUFSIZ] = {0};
                vGenAlphaCapital(acRandom, iLength);
                printf("%s ", acRandom);
            }
        }
        break;
    case 3:
        {
            for (int i = 0; i < iNumber; i++)
            {
                char acRandom[BUFSIZ] = {0};
                vGenAlphaLower(acRandom, iLength);
                printf("%s ", acRandom);
            }
        }
        break;
    default:
        {
            printf("Unknown type: %d\n\n", iType);
            vHelp();
            return 0;
        }
    }
    printf("\n");

    return 0;
}

void vHelp()
{
    printf("Usage: genRandom [options] [target] ...\n");
    printf("Options:\n");
    printf("\t-t      type: (0:random, 1:number, 2:AlphaCapital, 3:AlphaLower)\n");
    printf("\t-l      length: the length of each obj\n");
    printf("\t-n      number: the number of random obj\n");
    printf("\t-h      help\n");
}

void vGenRandom(char * str, const int iLen)
{
    char acBuf[BUFSIZ] = {0};
    strcpy(acBuf, g_acAlphaCapital);
    strcat(acBuf, g_acAlphaLower);
    strcat(acBuf, g_acNumber);
    strcat(acBuf, g_acCharacter);

    int iBufLen = strlen(acBuf);
    //printf("acBuf len = %d\n", iBufLen);
    int i;
    for (i = 0; i < iLen; i++) {
        str[i] = acBuf[ rand() % iBufLen ];
    }
    str[i] = '\0';
}

void vGenAlphaCapital(char * str, const int iLen)
{
    int i;
    for (i = 0; i < iLen; i++) {
        str[i] = g_acAlphaCapital[ rand() % ALPHA_NUM ];
    }
    str[i] = '\0';
}

void vGenAlphaLower(char * str, const int iLen)
{
    int i;
    for (i = 0; i < iLen; i++) {
        str[i] = g_acAlphaLower[ rand() % ALPHA_NUM ];
    }
    str[i] = '\0';
}

void vGenNumber(char * str, const int iLen)
{
    int i;
    for (i = 0; i < iLen; i++) {
        str[i] = g_acNumber[ rand() % NUMBER_NUM ];
    }
    str[i] = '\0';
}
```
