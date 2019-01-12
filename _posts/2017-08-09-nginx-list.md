---
layout: post
title: "[Nginx] list链表"
date: 2017-08-09
author: mdgsf
comments: true
categories: nginx
tags: nginx
description:
published: true #default true
---

源文件如下：

nginx-1.13.1\src\core\ngx_list.h

nginx-1.13.1\src\core\ngx_list.c

nginx中list和array感觉很像，还有内存池的结构。

### 结构体定义

```c
typedef struct ngx_list_part_s  ngx_list_part_t;

struct ngx_list_part_s {
    void             *elts;
    ngx_uint_t        nelts;
    ngx_list_part_t  *next;
};

typedef struct {
    ngx_list_part_t  *last; //指向最后一块内存
    ngx_list_part_t   part; //指向第一块内存
    size_t            size; //保存每一块内存中，每个元素的大小
    ngx_uint_t        nalloc; //保存每一块内存中，可以有多少个元素
    ngx_pool_t       *pool; //内存池
} ngx_list_t;
```

这里的链表，是把多个像 数组一样的空间 链接起来， 不是每一个元素链接的，估计是为了提高效率吧。

而且没有提供删除函数，有点神奇。

## ngx_list_create

```c
/*
@brief: 建立链表。
@param pool: 使用的内存池。
@param n[in]: 元素的数量
@param size[in]: 每个元素的大小，字节。
@return: 返回新创建的链表的指针。
*/
ngx_list_t *
ngx_list_create(ngx_pool_t *pool, ngx_uint_t n, size_t size)
{
    ngx_list_t  *list;

    list = ngx_palloc(pool, sizeof(ngx_list_t));
    if (list == NULL) {
        return NULL;
    }

    if (ngx_list_init(list, pool, n, size) != NGX_OK) {
        return NULL;
    }

    return list;
}
```

## ngx_list_init

```c
static ngx_inline ngx_int_t
ngx_list_init(ngx_list_t *list, ngx_pool_t *pool, ngx_uint_t n, size_t size)
{
    list->part.elts = ngx_palloc(pool, n * size);
    if (list->part.elts == NULL) {
        return NGX_ERROR;
    }

    list->part.nelts = 0;
    list->part.next = NULL;
    list->last = &list->part;
    list->size = size;
    list->nalloc = n;
    list->pool = pool;

    return NGX_OK;
}
```


## ngx_list_push

```c
/*
@brief: 从链表l中取出一个新的节点使用。
@param l[inout]: 链表。
@return: 返回指向新的节点的指针。
*/
void *
ngx_list_push(ngx_list_t *l)
{
    void             *elt;
    ngx_list_part_t  *last;

    last = l->last;

    if (last->nelts == l->nalloc) {

        /* the last part is full, allocate a new list part */

        last = ngx_palloc(l->pool, sizeof(ngx_list_part_t));
        if (last == NULL) {
            return NULL;
        }

        //分配一块新的内存块，这块内存块的大小和之前的是一样的。应该说每一块内存块的大小都是一样的。
        last->elts = ngx_palloc(l->pool, l->nalloc * l->size); 
        if (last->elts == NULL) {
            return NULL;
        }

        last->nelts = 0;
        last->next = NULL;

        l->last->next = last;
        l->last = last;
    }

    elt = (char *) last->elts + l->size * last->nelts;
    last->nelts++;

    return elt;
}
```














