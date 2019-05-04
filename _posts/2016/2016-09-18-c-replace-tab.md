---
layout: post
title:  "[C/C++] Replace Tab"
date:   2016-09-18
comments: true
categories: C/C++
tags: [C]
description:
published: true
---

```cpp
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <sys/types.h>
#include <dirent.h>
#include <unistd.h>
#include <errno.h>

#define JBUF_SIZE 4096
#define BBUF_SIZE 2048
#define MBUF_SIZE 1024
#define SBUF_SIZE 512
#define BUF_SIZE 256
#define LBUF_SIZE 128
#define UBUF_SIZE 64
#define NBUF_SIZE 32

int
iGetFileSize(FILE * fp)
{
    fseek(fp, 0, SEEK_END);
    int iFileSize = ftell(fp);
    rewind(fp);
    return iFileSize;
}

int
iGetFileData(const char * pcFile, char ** ppcContent, int * piContentSize)
{
    if(NULL == pcFile) {
        return -1;
    }

    FILE * fp = fopen(pcFile, "rb");
    if(NULL == fp) {
        printf("fopen %s failed\n", pcFile);
        return -1;
    }

    int iFileSize = iGetFileSize(fp);
    printf("iFileSize = %d\n", iFileSize);
    if(iFileSize <= 0) {
        printf("%s file size = %d\n", pcFile, iFileSize);
        return -1;
    }

    char * pcContent = (char*)malloc(iFileSize + 1);
    if(NULL == pcContent) {
        printf("malloc failed\n");
        return -1;
    }
    memset(pcContent, 0, iFileSize + 1);

    int iReadBytes = fread(pcContent, 1, iFileSize, fp);
    if(iReadBytes != iFileSize) {
        printf("fread %s failed\n", pcFile);
        return -1;
    }
    pcContent[iFileSize] = '\0';

    if(fclose(fp) != 0) {
        printf("fclose %s failed\n", pcFile);
        return -1;
    }

    *ppcContent = pcContent;
    *piContentSize = iFileSize;

    return 0;
}

int
iWriteToFile(const char * pcFileName, const char * pcContent, int iContentSize)
{
    if(NULL == pcFileName || NULL == pcContent) {
        return -1;
    }

    FILE * fp = fopen(pcFileName, "wb+");
    if(NULL == fp) {
        printf("fopen %s failed\n", pcFileName);
        return -1;
    }

    int iWriteBytes = fwrite(pcContent, iContentSize, 1, fp);
    if(iWriteBytes <= 0) {
        printf("fwrite %s failed\n", pcFileName);
        return -1;
    }

    if(fclose(fp) != 0) {
        printf("fclose %s failed\n", pcFileName);
        return -1;
    }

    return 0;
}

int
iReplaceTab(const char * pcStr, int iSize, char ** ppcNewContent, int * piNewSize)
{
    if(NULL == pcStr || iSize <= 0) {
        return -1;
    }

    int i = 0;
    int iTabNum = 0;
    for (i = 0; i < iSize; i++)
    {
        if(pcStr[i] == '\t')
        {
            ++iTabNum;
        }
    }
    int iNewSize = iSize + iTabNum*3 + 1;

    if(iTabNum == 0)
    {
        return 1;
    }

    char * pcNewStr = (char*)malloc(iNewSize);
    if(NULL == pcNewStr) {
        printf("malloc failed\n");
        return -1;
    }
    memset(pcNewStr, 0, iNewSize);

    int iIndex = 0;
    for (i = 0; i < iSize; i++)
    {
        if(pcStr[i] == '\t')
        {
            pcNewStr[iIndex++] = ' ';
            pcNewStr[iIndex++] = ' ';
            pcNewStr[iIndex++] = ' ';
            pcNewStr[iIndex++] = ' ';
        }
        else
        {
            pcNewStr[iIndex++] = pcStr[i];
        }
    }
    pcNewStr[iIndex] = '\0';

    *ppcNewContent = pcNewStr;
    *piNewSize = iIndex;

    return 0;
}

void vReplaceFileTab(const char * pcFileName)
{
    if(NULL == pcFileName) {
        printf("NULL == pcFileName\n");
        return ;
    }

    char * pcContent = NULL;
    int iContentSize = 0;
    int iRet = iGetFileData(pcFileName, &pcContent, &iContentSize);
    if(iRet != 0) {
        printf("iGetFileData failed\n");
        return ;
    }

    char * pcNewContent = NULL;
    int iNewSize = 0;
    iRet = iReplaceTab(pcContent, iContentSize, &pcNewContent, &iNewSize);
    if(iRet == 1)
    {
        //printf("There is no tab in %s\n", pcFileName);
        free(pcContent);
        return ;
    }
    else if(iRet != 0)
    {
        printf("NULL == pcNewContent\n");
        return ;
    }

    //printf("%s\n", pcContent);
    //printf("%s\n", pcNewContent);

    iRet = iWriteToFile(pcFileName, pcNewContent, iNewSize);
    if(iRet != 0) {
        printf("iWriteToFile failed\n");
    }

    if(pcContent != NULL) {
        free(pcContent);
    }
    if(pcNewContent != NULL) {
        free(pcNewContent);
    }
}

bool bIsMarkDownFile(const char * pcFileName)
{
    if(NULL == pcFileName)
    {
        return false;
    }

    int iLen = strlen(pcFileName);
    if(iLen <= 3)
    {
        return false;
    }

    if(pcFileName[iLen-1] == 'd' &&
       pcFileName[iLen-2] == 'm' &&
       pcFileName[iLen-3] == '.')
    {
        return true;
    }
    else
    {
        return false;
    }
}

void vReplaceTabInDirectory(const char * pcDirectory)
{
    if(NULL == pcDirectory) {
        return ;
    }

    DIR * dir = opendir(pcDirectory);
    if(NULL == dir) {
        printf("opendir:(%s) failed, errno=(%d,%s)\n", pcDirectory, errno, strerror(errno));
        return ;
    }

    struct dirent * ptr;
    while ( (ptr = readdir(dir)) != NULL )
    {
        if(DT_UNKNOWN == ptr->d_type)
        {
            continue;
        }
        else if(DT_DIR == ptr->d_type)
        {
            if(strcmp(".", ptr->d_name) == 0 ||
               strcmp("..", ptr->d_name) == 0)
            {
                continue;
            }
            char acNewDir[SBUF_SIZE] = {0};
            sprintf(acNewDir, "%s/%s", pcDirectory, ptr->d_name);
            vReplaceTabInDirectory(acNewDir);
        }
        else
        {
            if(bIsMarkDownFile(ptr->d_name))
            {
                char acFilePath[MBUF_SIZE] = {0};
                sprintf(acFilePath, "%s/%s", pcDirectory, ptr->d_name);
                vReplaceFileTab(acFilePath);
            }
            //printf("%d:%s/%s\n", ptr->d_type, pcDirectory, ptr->d_name);
        }
    }
}

int main()
{
    vReplaceTabInDirectory(".");
    return 0;
}
```
