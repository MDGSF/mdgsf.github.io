---
layout: post
title:  "[链表] 单链表是否有环并如何找到环入口"
date:   2016-04-11
comments: true
categories: Art
tags: c, list
description:
published: true
---


## 1、如何判断一个链表是不是有环？

方法一：在链表中增加一个域visited，初始化都为0，从链表的头部开始走，每走过一个链表就标记visited为1，如果要访问的下一个节点的visited域为1，那么证明链表中有环。

方法二：如果不能增加域，可以设置一个map，将已经访问的链表节点依次放入到map中，在访问下一个节点时，如果这个节点的地址已经在map中，那么也能证明链表中有环。

方法三：设置两个指针fast和slow，slow一次走一下，fast一次走两下，如果他们相遇，则说明有环。

## 2、如果链表为存在环，如果找到环的入口点？

走一步的指针叫slow，走两步的叫fast。

相遇的时候，slow共移动了s步，fast共移动了2s步，这个是显而易见的。

定义a如下： 链表头移动a步到达入口点。

定义x如下： 入口点移动x步到达相遇点。

定义r如下： 从环中的某一点移动r步，又到达的这一点，也就是转了一圈的意思，r就是环一圈的节点个数。

定义t如下：　从相遇点移动到入口点的移动步数。

定义L如下： 从链表头移动L步，又到达了相遇点。也就是遍历完链表之后，通过最后一个节点的指针，又移动到了链表中的某一点。

其中Ｌ　＝　a + r  =  a + x + t

那么slow和fast相遇了，fast必然比slow多走了n个圈，也就是 n*r 步，那么

s = a + x   (slow一定走不完一圈，用笔在纸张上画画就明白了。)

2s = s + n*r ， 可得  s = n*r

将s=a+x，带入s =n*r，可得 a+x = n*r, 也就是 a+x = (n-1)*r + r 　　

从表头移动到入口点，再从入口点移动到入口点，也就是移动了整个链表的距离，即是 L =  a + r , 所以r = L - a

所以   a+x = (n-1)*r + L - a ,   于是 a  = (n-1)*r + L - a - x = (n-1)*r + t

也就是说，从表头到入口点的距离，等于从相遇点继续遍历又到入口点的距离。

所以，从表头设立一个指针，从相遇点设立一个指针，两个同时移动，必然能够在入口点相遇，这样，就求出了相遇点。

```cpp
typedef struct _SListNode {
    int m_iVal;
    void *m_pData;
    _SListNode *next;
    _SListNode() : m_iVal(0), m_pData(NULL), next(NULL) {}
    _SListNode(int iVal) : m_iVal(iVal), m_pData(NULL), next(NULL) {}
}SListNode, *PList;

/*
 *  if list has loop, return loop port.
 *  else return NULL.
 */
PList FindLoopPort(PList head)
{
    PList fast = head;
    PList slow = head;

    while (fast != NULL && fast->next != NULL) {
        fast = fast->next->next;
        slow = slow->next;
        if(slow == fast) {
            break;
        }
    }

    if(fast == NULL || fast->next == NULL) {
        return NULL;
    }

    slow = head;
    while (slow != fast) {
        slow = slow->next;
        fast = fast->next;
    }

    return slow;
}
```





### 链接

[单链表是否有环并如何找到环入口](http://www.cnblogs.com/cyttina/archive/2012/10/28/2743760.html)
