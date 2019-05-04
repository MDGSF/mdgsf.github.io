---
layout: post
title:  "二分查找"
date:   2016-05-04
comments: true
categories: Art
tags: [Art,Algorithm,Binarysearch]
description:
published: true
---

### 什么是二分查找？

就是在一个有序的集合 `a` 里面，要查找一个数字 `v`。

我们通过和中间的那个数字 `a[mid]` 对比，

如果 `a[mid]` > `v`，说明 `v` 在集合的前半部分，即 `a[0]` ~ `a[mid-1]`。

如果 `a[mid]` < `v`，说明 `v` 在集合的后半部分，即 `a[mid+1]` ~ `a[n-1]`。

如果 `a[mid]` == `v`，则找到数字。

### 举例 - 猜数字游戏

我随机写一个 0 到 99 之间的数字，然后你来猜我写的是什么？你每次猜的时候，我会告诉你猜的是太大了，还是太小了，直到你猜中为止。

假设我写的数字是 86。

用二次查找的思想猜数字的过程如下：

次数    | 猜测的范围 | 中间数字 | 对比大小
第 1 次 | 0  - 99  |   49    | 49 < 86
第 2 次 | 50 - 99  |   74    | 74 < 86
第 3 次 | 75 - 99  |   87    | 87 > 86
第 4 次 | 75 - 86  |   80    | 80 < 86
第 5 次 | 81 - 86  |   83    | 83 < 86
第 6 次 | 84 - 86  |   85    | 85 < 86
第 7 次 |   86     |   86    | 86 = 86

一共猜了 7 次，每次猜过之后，剩下的数字范围就少了一半。

第 1 次，猜的范围有 100 个数字。

第 2 次，猜的范围就只剩下 50 个数字了。

第 3 次，则只有 25 个数字了，这就是二分查找。

### 时间复杂度分析

```
假设有 n 个元素，查找了 k 次才找到。

第 1 次查找:   n/2
第 2 次查找:   ( n/2 ) / 2 = n/4 = n/(2^2)
第 3 次查找:   ( n/(2^2) ) / 2 = n/(2^3)
第 4 次查找:   ( n/(2^3) ) / 2 = n/(2^4)
...
第 k 次查找:   n/(2^k)

n/(2^k) 代表的是还剩下的元素的个数
n/(2^k) >= 1
当 n/(2^k) == 1 时，查找结束。

所以令 n/(2^k) = 1 ， 即
==>   n = 2^k
==>   k = log(n) //以2为基

所以时间复杂度为 O( log(n) )
```

### 代码链接地址

[https://github.com/MDGSF/algo/blob/master/go/binarysearch/binarysearch.go](https://github.com/MDGSF/algo/blob/master/go/binarysearch/binarysearch.go)

[https://github.com/MDGSF/algo/blob/master/c-cpp/binarysearch/binarysearch.cpp](https://github.com/MDGSF/algo/blob/master/c-cpp/binarysearch/binarysearch.cpp)

### 二分查找，非递归版本

```golang
// BinarySearch binary search
// 二分查找，非递归版本
func BinarySearch(a []int, v int) int {
    if len(a) == 0 {
        return -1
    }

    low := 0
    high := len(a) - 1
    for low <= high {
        mid := low + (high-low)/2
        if a[mid] == v {
            return mid
        } else if a[mid] > v {
            high = mid - 1
        } else {
            low = mid + 1
        }
    }
    return -1
}
```

### 二分查找，递归版本

```golang
// BinarySearchRecursive binary search
// 二分查找，递归版本
func BinarySearchRecursive(a []int, v int) int {
    if len(a) == 0 {
        return -1
    }
    return BinarySearchRecursiveInner(a, v, 0, len(a)-1)
}

func BinarySearchRecursiveInner(a []int, v int, low int, high int) int {
    if low > high {
        return -1
    }
    mid := low + (high-low)/2
    if a[mid] == v {
        return mid
    } else if a[mid] > v {
        return BinarySearchRecursiveInner(a, v, low, mid-1)
    } else {
        return BinarySearchRecursiveInner(a, v, mid+1, high)
    }
}
```

### C++ 代码

```cpp
int iBinarySearch(int aiNum[], int iNumSize, int iVal)
{
    if(NULL == aiNum || iNumSize <= 0)
    {
        return -1;
    }

    int iStart = 0;
    int iEnd = iNumSize - 1;

    while (iStart <= iEnd)
    {
        int iMid = iStart + ((iEnd - iStart) >> 1);
        if(aiNum[iMid] < iVal)
        {
            iStart = iMid + 1;
        }
        else if(aiNum[iMid] > iVal)
        {
            iEnd = iMid - 1;
        }
        else
        {
            return iMid;
        }
    }

    return -1;
}
```
