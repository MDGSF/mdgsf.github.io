---
layout: post
title: "[排序] 希尔排序"
date: 2016-04-08
author: mdgsf
comments: true
categories: Art
tags: c, sort
description:
published: true #default true
---

希尔排序的实质就是分组插入排序，该方法又称缩小增量排序，因DL．Shell于1959年提出而得名。

该方法的基本思想是：先将整个待排元素序列分割成若干个子序列（由相隔某个“增量”的元素组成的）分别进行直接插入排序，然后依次缩减增量再进行排序，待整个序列中的元素基本有序（增量足够小）时，再对全体元素进行一次直接插入排序。因为直接插入排序在元素基本有序的情况下（接近最好情况），效率是很高的，因此希尔排序在时间效率上比前两种方法有较大提高。

<hr />

以n=10的一个数组49, 38, 65, 97, 26, 13, 27, 49, 55, 4为例

**第一次 gap = 10 / 2 = 5**

```
49  38  65  97  26  13  27  49  55  4

1A                  1B

    2A                  2B

        3A                  3B

            4A                  4B

                5A                  5B
```

1A,1B，2A,2B等为分组标记，数字相同的表示在同一组，大写字母表示是该组的第几个元素， 每次对同一组的数据进行直接插入排序。即分成了五组(49, 13) (38, 27) (65, 49)  (97, 55)  (26, 4)这样每组排序后就变成了(13, 49)  (27, 38)  (49, 65)  (55, 97)  (4, 26)，下同。

**第二次 gap = 5 / 2 = 2**

排序后

```
13  27  49  55  4   49  38  65  97  26

1A      1B      1C      1D      1E

    2A      2B      2C      2D      2E
```

**第三次 gap = 2 / 2 = 1**

```
4   26  13  27  38  49  49  55  97  65

1A  1B  1C  1D  1E  1F  1G  1H  1I  1J
```

**第四次 gap = 1 / 2 = 0 排序完成得到数组：**

```
4   13  26  27  38  49  49  55  65  97
```

### Code

```cpp
#include <iostream>
#include <stdio.h>
using namespace std;

void vPrint(int a[], int n)
{
    for (int i = 0; i < n; i++) {
        printf("%d ", a[i]);
    }
    printf("\n");
}

void vInsertSortWithGap(int a[], int n, int gap, int iStartIndex)
{
    for (int i = iStartIndex; i < n; i += gap) {
        for (int j = i; j >= 0; j -= gap) {
            if((j-gap >= 0) && (a[j] < a[j-gap])) {
                swap(a[j], a[j-gap]);
            }
        }
    }
}

void shellSort(int a[], int n)
{
    for (int gap = n/2; gap > 0; gap /= 2)
    {
        for (int i = 0; i < gap; i++)
        {
            vInsertSortWithGap(a, n, gap, i);
        }
    }
}

int main()
{
    int a[] = {5,1,3,4,9,7,6,10,2,8};
    vPrint(a, 10);
    shellSort(a, 10);
    vPrint(a, 10);
    return 0;
}
```


### 链接

[白话经典算法系列之三 希尔排序](http://blog.csdn.net/morewindows/article/details/6668714)
