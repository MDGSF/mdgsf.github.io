---
layout: post
title:  "[链表] 判断两个链表是否相交并找出交点 "
date:   2016-04-11
comments: true
categories: Art
tags: [C,List]
description:
published: true
---

问题描述：

一个比较经典的问题，判断两个链表是否相交，如果相交找出他们的交点。

<img src="{{ site.url }}/images/201604/11_01.png" alt="11_01" />

思路：

1、碰到这个问题，第一印象是采用hash来判断，将两个链表的节点进行hash，然后判断出节点，这种想法当然是可以的。

2、当然采用暴力的方法也是可以的，遍历两个链表，在遍历的过程中进行比较，看节点是否相同。

3、第三种思路是比较奇特的，在编程之美上看到的。先遍历第一个链表到他的尾部，然后将尾部的next指针指向第二个链表(尾部指针的next本来指向的是null)。这样两个链表就合成了一个链表，判断原来的两个链表是否相交也就转变成了判断新的链表是否有环的问题了：即判断单链表是否有环？

这样进行转换后就可以从链表头部进行判断了，其实并不用。通过简单的了解我们就很容易知道，如果新链表是有环的，那么原来第二个链表的头部一定在环上。因此我们就可以从第二个链表的头部进行遍历的，从而减少了时间复杂度(减少的时间复杂度是第一个链表的长度)。

下图是一个简单的演示：

<img src="{{ site.url }}/images/201604/11_02.png" alt="11_02" />

这种方法可以判断两个链表是否相交，但不太容易找出他们的交点。

4、仔细研究两个链表，如果他们相交的话，那么他们最后的一个节点一定是相同的，否则是不相交的。因此判断两个链表是否相交就很简单了，分别遍历到两个链表的尾部，然后判断他们是否相同，如果相同，则相交；否则不相交。示意图如下：

<img src="{{ site.url }}/images/201604/11_03.png" alt="11_03" />

判断出两个链表相交后就是判断他们的交点了。假设第一个链表长度为len1，第二个问len2，然后找出长度较长的，让长度较长的链表指针向后移动**(len1 - len2)** (len1-len2的绝对值)，然后在开始遍历两个链表，两个链表每次移动一步，判断节点是否相同即可。


```cpp
/*
 *  if head1, head2 has intersection, return intersection point,
 *  else return NULL.
 */
PList pFindIntersectionPoint(PList head1, PList head2)
{
    if(NULL == head1 || NULL == head2) {
        return NULL;
    }

    PList p1 = head1;
    PList p2 = head2;
    int iLen1 = 0;
    int iLen2 = 0;
    int iDiffLen = 0;

    while (p1 != NULL) {
        iLen1++;
        p1 = p1->next;
    }
    while (p2 != NULL) {
        iLen2++;
        p2 = p2->next;
    }

    if(p1 != p2) { //last point is different.
        return NULL;
    }

    if(iLen1 > iLen2) {
        p1 = head1;
        p2 = head2;
    } else {
        p1 = head2;
        p2 = head1;
    }

    iDiffLen = iAbs(iLen1 - iLen2);
    for (int i = 0; i < iDiffLen; i++) {
        p1 = p1->next;
    }

    while (p1 != p2) {
        p1 = p1->next;
        p2 = p2->next;
    }

    return p1;
}
```


### 链接

[判断两个链表是否相交并找出交点 ](http://blog.csdn.net/jiqiren007/article/details/6572685)
