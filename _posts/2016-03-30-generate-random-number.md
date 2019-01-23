---
layout: post
title:  "[C/C++] Generate random number"
date:   2016-03-30
comments: true
categories: C/C++
tags: [Rand]
description:
published: true
---


```cpp
#include <iostream>
#include <ctime>
#include <stdio.h>
#include <stdlib.h>

using namespace std;

const int MAXNUMBER = 10000000;
const int MINNUMBER = 1000;

int main()
{
    //typedef long clock_t
    clock_t lStartTime = clock();

    srand(unsigned(time(0)));
    freopen("data.txt", "w", stdout);
    unsigned long ulData = 0;
    for (int i=0; i<1000000; i++)
    {
        ulData = rand() % (MAXNUMBER - MINNUMBER + 1) + MINNUMBER;
        cout << ulData << ' '; //output the data to data.txt
    }
    fclose(stdout);

    freopen("time.txt", "w", stdout);
    cout << "Elapsed time: " << double(clock() - lStartTime) / CLOCKS_PER_SEC
        << 's' << endl;
    fclose(stdout);

    return 0;
}
```
