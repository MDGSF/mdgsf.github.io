---
layout: post
title: "[Nginx] queue队列"
date: 2017-08-09
author: mdgsf
comments: true
categories: nginx
tags: nginx
description:
published: true #default true
---

源文件如下：

nginx-1.13.1\src\core\ngx_queue.h

nginx-1.13.1\src\core\ngx_queue.c


## 两种不同的链表类型

<img src="{{ site.url }}/images/2017/08/0901.png" alt="0901" />

我发现很多比较底层的代码都是使用第二种方式，而且喜欢用宏定义。

nginx_queue这里也是用第二种方式。

## 数据结构

```c
typedef struct ngx_queue_s  ngx_queue_t;

struct ngx_queue_s {
    ngx_queue_t  *prev;
    ngx_queue_t  *next;
};
```

## ngx_queue_init

初始化一个节点

```c
#define ngx_queue_init(q)                                                     \
    (q)->prev = q;                                                            \
    (q)->next = q
```

## ngx_queue_empty

判断该队列是不是空。

```c
#define ngx_queue_empty(h)                                                    \
    (h == (h)->prev)
```


## ngx_queue_insert_head

往队列h的头部插入节点x，也就是插入到h的下一个节点。

h是队列的头部，是一个空节点，也就是哨兵节点。

```c
#define ngx_queue_insert_head(h, x)                                           \
    (x)->next = (h)->next;                                                    \
    (x)->next->prev = x;                                                      \
    (x)->prev = h;                                                            \
    (h)->next = x

#define ngx_queue_insert_after   ngx_queue_insert_head
```


## ngx_queue_insert_tail

往队列h的尾部插入节点x，因为是双向链表，所以尾部就是h的前一个节点。

```c
#define ngx_queue_insert_tail(h, x)                                           \
    (x)->prev = (h)->prev;                                                    \
    (x)->prev->next = x;                                                      \
    (x)->next = h;                                                            \
    (h)->prev = x
```

## 

```c
//获取第一个节点
#define ngx_queue_head(h)                                                     \
    (h)->next


//获取最后一个节点
#define ngx_queue_last(h)                                                     \
    (h)->prev


//获取哨兵节点
#define ngx_queue_sentinel(h)                                                 \
    (h)


//获取该节点的下一个节点
#define ngx_queue_next(q)                                                     \
    (q)->next


//获取该节点的前一个节点
#define ngx_queue_prev(q)                                                     \
    (q)->prev
```


## ngx_queue_remove

删除节点x

```c
#define ngx_queue_remove(x)                                                   \
    (x)->next->prev = (x)->prev;                                              \
    (x)->prev->next = (x)->next
```


## ngx_queue_split

切割队列，h是队列的哨兵节点，q是该队列的内部节点，n是外部一个单独的节点，n不属于该队列。

```c
#define ngx_queue_split(h, q, n)                                              \
    (n)->prev = (h)->prev;                                                    \
    (n)->prev->next = n;                                                      \
    (n)->next = q;                                                            \
    (h)->prev = (q)->prev;                                                    \
    (h)->prev->next = h;                                                      \
    (q)->prev = n;
```


## ngx_queue_add

连接两个链表，把链表n连接到链表h的尾部。

```c
#define ngx_queue_add(h, n)                                                   \
    (h)->prev->next = (n)->next;                                              \
    (n)->next->prev = (h)->prev;                                              \
    (h)->prev = (n)->prev;                                                    \
    (h)->prev->next = h;
```


## ngx_queue_data

通过一个指向链表节点的指针，获取到该节点对应的结构体的指针。

```c
#define ngx_queue_data(q, type, link)                                         \
    (type *) ((u_char *) q - offsetof(type, link))

#define offsetof(s,m) ((size_t)&(((s*)0)->m))
```

不理解的话，看下面的这个链接，在文章的最底部。

<a href="http://mdgsf.github.io/linux-kernel/2017/07/21/linux-kernel-list.html" target="_blank">http://mdgsf.github.io/linux-kernel/2017/07/21/linux-kernel-list.html</a>


## ngx_queue_middle

```c
/*
 * find the middle queue element if the queue has odd number of elements
 * or the first element of the queue's second part otherwise
 */

/*
@brief: 查找队列的中间节点。
就是用两个指针，第一个指针每次走两步，第二个每次走一步，当第一个指针走到尾部时，第二个指针就是中间节点。
*/
ngx_queue_t *
ngx_queue_middle(ngx_queue_t *queue)
{
    ngx_queue_t  *middle, *next;

    middle = ngx_queue_head(queue);

    if (middle == ngx_queue_last(queue)) {
        return middle;
    }

    next = ngx_queue_head(queue);

    for ( ;; ) {
        middle = ngx_queue_next(middle);

        next = ngx_queue_next(next);

        if (next == ngx_queue_last(queue)) {
            return middle;
        }

        next = ngx_queue_next(next);

        if (next == ngx_queue_last(queue)) {
            return middle;
        }
    }
}
```



## ngx_queue_sort

```c
/* the stable insertion sort */
//稳定的插入排序。
void
ngx_queue_sort(ngx_queue_t *queue,
    ngx_int_t (*cmp)(const ngx_queue_t *, const ngx_queue_t *))
{
    ngx_queue_t  *q, *prev, *next;

    q = ngx_queue_head(queue);

    if (q == ngx_queue_last(queue)) {
        return;
    }

    for (q = ngx_queue_next(q); q != ngx_queue_sentinel(queue); q = next) {

        prev = ngx_queue_prev(q);
        next = ngx_queue_next(q);

        ngx_queue_remove(q);

        do {
            if (cmp(prev, q) <= 0) {
                break;
            }

            prev = ngx_queue_prev(prev);

        } while (prev != ngx_queue_sentinel(queue));

        ngx_queue_insert_after(prev, q);
    }
}
```





