---
layout: post
title: "[字符串] 全排列"
date: 2016-04-07
author: mdgsf
comments: true
categories: Art
tags: [C,String,Permutation]
description:
published: true #default true
---

## 一、字符串的排列

### Question

用C++写一个函数, 如 Foo(const char *str), 打印出 str 的全排列,

如 abc 的全排列: abc, acb, bca, bac, cab, cba

为方便起见，用123来示例下。123的全排列有123、132、213、231、312、321这六种。首先考虑213和321这二个数是如何得出的。显然这二个都是123中的1与后面两数交换得到的。然后可以将123的第二个数和每三个数交换得到132。同理可以根据213和321来得231和312。因此可以知道——全排列就是从第一个数字起每个数分别与它后面的数字交换。

```
Permutation(abc) = a + Permutation(bc)
                   b + Permutation(ac)
                   c + Permutation(ba)

Permutation(abcd) = a + Permutation(bcd)
                    b + Permutation(acd)
                    c + Permutation(bad)
                    d + Permutation(bca)
```

<hr />

```
a: a                        1
ab: ab ba                   2*1=2
abc: abc acb                2*3=6
     bac bca
     cba cab
abcd: abcd a(...) => 6      6*4=24
      bacd b(...) => 6
      cbad c(...) => 6
      dbca d(...) => 6
abcde: ...                  24*5= 120
```

## 递归代码

```cpp
#include <iostream>
#include <stdio.h>
#include <assert.h>
#include <string.h>
using namespace std;

void Permutation(char * pStr, char * pBegin)
{
    assert(pStr && pBegin);

    if(*pBegin == '\0') {
        printf("%s\n", pStr);
    } else {
        for (char * pCh = pBegin; *pCh != '\0'; pCh++)
        {
            swap(*pBegin, *pCh);
            Permutation(pStr, pBegin+1);
            swap(*pBegin, *pCh);
        }
    }
}

void Permutation2(char * pStr, int k, int m)
{
    assert(pStr);

    if(k == m) {
        static int num = 0;
        printf("num=%d, %s\n", ++num, pStr);
    } else {
        for (int i = k; i <= m; i++)
        {
            swap(*(pStr+k), *(pStr+i));
            Permutation2(pStr, k+1, m);
            swap(*(pStr+k), *(pStr+i));
        }
    }
}

int main()
{
    char str[] = "abc";
    Permutation(str, str);
    Permutation2(str, 0, strlen(str)-1);
    return 0;
}
```

如果字符串中有重复字符的话，上面的那个方法肯定不会符合要求的，因此现在要想办法来去掉重复的数列。

## 二、去掉重复的全排列的递归实现

由于全排列就是从第一个数字起每个数分别与它后面的数字交换。我们先尝试加个这样的判断——如果一个数与后面的数字相同那么这二个数就不交换了。如122，第一个数与后面交换得212、221。然后122中第二数就不用与第三个数交换了，但对212，它第二个数与第三个数是不相同的，交换之后得到221。与由122中第一个数与第三个数交换所得的221重复了。所以这个方法不行。

换种思维，对122，第一个数1与第二个数2交换得到212，然后考虑第一个数1与第三个数2交换，此时由于第三个数等于第二个数，所以第一个数不再与第三个数交换。再考虑212，它的第二个数与第三个数交换可以得到解决221。此时全排列生成完毕。
这样我们也得到了在全排列中去掉重复的规则——去重的全排列就是从第一个数字起每个数分别与它后面非重复出现的数字交换。下面给出完整代码：

## Code

```cpp
#include <iostream>
#include <stdio.h>
#include <assert.h>
#include <string.h>
using namespace std;

//在[nBegin,nEnd)区间中是否有字符与下标为pEnd的字符相等
bool IsSwap(char * pBegin, char * pEnd)
{
    for (char * p = pBegin; p < pEnd; p++)
    {
        if(*p == *pEnd)
            return false;
    }
    return true;
}

void Permutation(char * pStr, char * pBegin)
{
    assert(pStr && pBegin);

    if(*pBegin == '\0') {
        static int num = 0;
        printf("num=%d, %s\n", ++num, pStr);
    } else {
        for (char * pCh = pBegin; *pCh != '\0'; pCh++)//第pBegin个数分别与它后面的数字交换就能得到新的排列
        {
            if(IsSwap(pBegin, pCh)) {
                swap(*pBegin, *pCh);
                Permutation(pStr, pBegin+1);
                swap(*pBegin, *pCh);
            }
        }
    }
}

int main()
{
    char str[] = "baa";
    Permutation(str, str);
    return 0;
}
```

### 三、全排列的非递归实现

要考虑全排列的非递归实现，先来考虑如何计算字符串的下一个排列。如"1234"的下一个排列就是"1243"。只要对字符串反复求出下一个排列，全排列的也就迎刃而解了。

如何计算字符串的下一个排列了？来考虑"926520"这个字符串，我们从后向前找第一双相邻的递增数字，"20"、"52"都是非递增的，"26 "即满足要求，称前一个数字2为替换数，替换数的下标称为替换点，再从后面找一个比替换数大的最小数（这个数必然存在），0、2都不行，5可以，将5和2交换得到"956220"，然后再将替换点后的字符串"6220"颠倒即得到"950226"。
对于像“4321”这种已经是最“大”的排列，采用STL中的处理方法，将字符串整个颠倒得到最“小”的排列"1234"并返回false。

这样，只要一个循环再加上计算字符串下一个排列的函数就可以轻松的实现非递归的全排列算法。按上面思路并参考STL中的实现源码，不难写成一份质量较高的代码。值得注意的是在循环前要对字符串排序下，可以自己写快速排序的代码（请参阅《白话经典算法之六 快速排序 快速搞定》），也可以直接使用VC库中的快速排序函数。下面列出完整代码：

```cpp
#include <iostream>
#include <algorithm>
#include <cstring>
#include <assert.h>
#include <stdio.h>
using namespace std;

void Reverse(char *pBegin, char *pEnd)
{
    while (pBegin < pEnd)
        swap(*pBegin++, *pEnd--);
}

int cmp(const void * a, const void * b)
{
    return int(*(char*)a - *(char*)b);
}

bool Next_permutation(char a[])
{
    assert(a);
    char * pEnd = a + strlen(a) - 1;
    if(pEnd == a) {
        return false;
    }

    char * p = pEnd;
    char * q = NULL;
    char * pFind = NULL;
    while (p != a)
    {
        q = p;
        p--;
        if(*p < *q) //找降序的相邻2数,前一个数即替换数
        {
            pFind = pEnd; //从后向前找比替换点大的第一个数
            while (*pFind < *p) {
                --pFind;
            }
            swap(*p, *pFind);
            Reverse(q, pEnd); //替换点后的数全部反转
            return true;
        }
    }
    Reverse(a, pEnd); //如果没有下一个排列,全部反转后返回false
    return false;
}

int main()
{
    char str[] = "abcd";
    int iNum = 1;
    qsort(str, strlen(str), sizeof(char), cmp);
    do {
        printf("iNum=%d, %s\n", iNum++, str);
    } while(Next_permutation(str));
    return 0;
}
```

**至此我们已经运用了递归与非递归的方法解决了全排列问题，总结一下就是:**

1. 全排列就是从第一个数字起每个数分别与它后面的数字交换。
2. 去重的全排列就是从第一个数字起每个数分别与它后面非重复出现的数字交换。
3. 全排列的非递归就是由后向前找替换数和替换点，然后由后向前找第一个比替换数大的数与替换数交换，最后颠倒替换点后的所有数据。

## 二、字符串的组合

题目：输入一个字符串，输出该字符串中字符的所有组合。举个例子，如果输入abc，它的组合有a、b、c、ab、ac、bc、abc。
上面我们详细讨论了如何用递归的思路求字符串的排列。同样，本题也可以用递归的思路来求字符串的组合。

假设我们想在长度为n的字符串中求m个字符的组合。我们先从头扫描字符串的第一个字符。针对第一个字符，我们有两种选择：第一是把这个字符放到组合中去，接下来我们需要在剩下的n-1个字符中选取m-1个字符；第二是不把这个字符放到组合中去，接下来我们需要在剩下的n-1个字符中选择m个字符。这两种选择都很容易用递归实现。下面是这种思路的参考代码：

```cpp
#include <iostream>
#include <stdio.h>
#include <vector>
#include <assert.h>
#include <string.h>
using namespace std;

void Combination(char * string, int number, vector<char> & result)
{
    assert(string != NULL);

    if(number == 0) {
        static int num = 1;
        printf("num=%d, ", num++);
        vector<char>::iterator it = result.begin();
        for (; it != result.end(); ++it) {
            printf("%c", *it);
        }
        printf("\n");
        return ;
    }

    if(*string == '\0') {
        return ;
    }

    result.push_back(*string);
    Combination(string+1, number-1, result);
    result.pop_back();
    Combination(string+1, number, result);
}

void Combination(char * string)
{
    assert(string != NULL);
    vector<char> result;
    int iLen = strlen(string);
    for (int i = 1; i <= iLen; i++) {
        Combination(string, i, result);
    }
}

int main()
{
    char str[] = "abcd";
    Combination(str);
    return 0;
}
```

## 位运算解法

```cpp
#include<iostream>
using namespace std;

int a[] = {1,3,5,4,6};
char str[] = "abcde";

void print_subset(int n , int s)
{
    printf("{");
    for(int i = 0 ; i < n ; ++i)
    {
        if( s&(1<<i) )
            printf("%c ",str[i]);
    }
    printf("}\n");
}

void subset(int n)
{
    for(int i= 0 ; i < (1<<n) ; ++i)
    {
        print_subset(n,i);
    }
}

int main(void)
{
    subset(5);
    return 0;
}
```

### 字符串全排列扩展----八皇后问题

题目：在8×8的国际象棋上摆放八个皇后，使其不能相互攻击，即任意两个皇后不得处在同一行、同一列或者同一对角斜线上。下图中的每个黑色格子表示一个皇后，这就是一种符合条件的摆放方法。请求出总共有多少种摆法。

<img src="{{ site.url }}/images/201604/07_04.png" alt="07_04" />

由于八个皇后的任意两个不能处在同一行，那么这肯定是每一个皇后占据一行。于是我们可以定义一个数组ColumnIndex[8]，数组中第i个数字表示位于第i行的皇后的列号。先把ColumnIndex的八个数字分别用0-7初始化，接下来我们要做的事情就是对数组ColumnIndex做全排列。由于我们是用不同的数字初始化数组中的数字，因此任意两个皇后肯定不同列。我们只需要判断得到的每一个排列对应的八个皇后是不是在同一对角斜线上，也就是数组的两个下标i和j，是不是i-j==ColumnIndex[i]-ColumnIndex[j]或者j-i==ColumnIndex[i]-ColumnIndex[j]。

```cpp
#include <stdio.h>
#include <iostream>
using namespace std;

#define QUEEN_NUM 8

int g_iNumber = 0;

void vPrint(int aiColumnIndex[], int iLen)
{
    printf("%d\n", g_iNumber);
    for (int i = 0; i < iLen; i++)
        printf("%d ", aiColumnIndex[i]);
    printf("\n");
}

bool bIsValid(int aiColumnIndex[], int iLen)
{
    for (int i = 0; i < iLen; i++)
    {
        for (int j = i + 1; j < iLen; j++)
        {
            if(i-j == aiColumnIndex[i] - aiColumnIndex[j] ||
                    j-i == aiColumnIndex[i] - aiColumnIndex[j]) {
                return false;
            }
        }
    }
    return true;
}

void vPermutation(int aiColumnIndex[], int iLen, int iIndex)
{
    if(iIndex == iLen) {
        if(bIsValid(aiColumnIndex, iLen)) {
            ++g_iNumber;
            vPrint(aiColumnIndex, iLen);
        }
    } else {
        for (int i = iIndex; i < iLen; i++)
        {
            swap(aiColumnIndex[iIndex], aiColumnIndex[i]);
            vPermutation(aiColumnIndex, iLen, iIndex+1);
            swap(aiColumnIndex[iIndex], aiColumnIndex[i]);
        }
    }
}

void vEightQueen()
{
    int aiColumnIndex[QUEEN_NUM];
    for (int i = 0; i < QUEEN_NUM; i++) {
        aiColumnIndex[i] = i;
    }
    vPermutation(aiColumnIndex, QUEEN_NUM, 0);
}

int main()
{
    vEightQueen();
    return 0;
}
```

### 题目：输入两个整数n和m，从数列1,2,3...n中随意取几个数，使其和等于m，要求列出所有的组合。

```cpp
#include <iostream>
#include <stdio.h>
#include <list>
using namespace std;

list<int> list1;

void find_factor(int sum, int n)
{
    if(n <= 0 || sum <= 0) {
        return ;
    }

    if(sum == n) //输出找到的数
    {
        list1.reverse();
        for (list<int>::iterator it=list1.begin();
                it != list1.end();
                ++it) {
            cout<<*it<< "+";
        }
        cout << n << endl;
        list1.reverse();
    }

    list1.push_front(n);
    find_factor(sum-n, n-1); //n放在里面
    list1.pop_front();
    find_factor(sum, n-1); //n不放在里面
}

int main()
{
    int sum, n;
    cin>>sum>>n;
    find_factor(sum, n);
    return 0;
}
```

### 链接

[http://blog.csdn.net/jfkidear/article/details/50700195](http://blog.csdn.net/jfkidear/article/details/50700195)
