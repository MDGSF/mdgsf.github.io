---
layout: post
title:  "[C] Windows下使用getopt"
date:   2017-12-01
comments: true
categories: c
tags: c
description: ""
published: true
---


```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include "getopt.h"

int main(int argc, char ** argv)
{
    int c;
    while ((c = getopt(argc, argv, "i:p:abc:d:012")) != -1)
    {
        printf("c = %c\n", c);
        int this_option_optind = optind ? optind : 1;
        switch (c)
        {
        case 'i':
        {
            char acIP[128] = { 0 };
            strcpy(acIP, optarg);
            printf("acIP = %s\n", acIP);
            break;
        }
        case 'p':
        {
            int port = atoi(optarg);
            printf("port = %d\n", port);
            break;
        }
        case '0':
        case '1':
        case '2':
            break;
        case 'a':
            break;
        case 'b':
            break;
        case 'c':
            printf("option c with value %s\n", optarg);
            break;
        case 'd':
            printf("option d with value %s\n", optarg);
            break;
        default:
            printf("Unknown option.\n");
            break;
        }
    }

    if (optind < argc)
    {
        printf("non-option ARGV-elements: ");
        while (optind < argc)
        {
            printf("%s ", argv[optind++]);
        }
        printf("\n");
    }

    return 0;
}
```

https://github.com/alex85k/wingetopt

https://en.wikipedia.org/wiki/Getopt

