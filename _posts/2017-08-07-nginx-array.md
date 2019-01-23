---
layout: post
title: "[Nginx] array数组"
date: 2017-08-07
author: mdgsf
comments: true
categories: nginx
tags: [Nginx]
description:
published: true #default true
---


### 结构体定义

```c
/*
elts: 指向数组的指针。
nelts: 数组中实际元素的个数。
size: 数组中每个元素的大小。
nalloc: 数组中最多存储的元素的个数。
pool: 数组就是从这个内存池中分配内存的。

size * nalloc 就是数组实际使用的字节数。
*/
typedef struct {
    void        *elts;
    ngx_uint_t   nelts;
    size_t       size;
    ngx_uint_t   nalloc;
    ngx_pool_t  *pool;
} ngx_array_t;
```


### ngx_array_create

```c
/*
@brief: 从内存池p中分配一个数组，数组的大小为 n * size 字节。
@param p[inout]: 内存池。
@param n[in]: 数组的元素个数。
@param size[in]: 每个元素的大小。
@return: 返回指向数组对象的指针。
*/
ngx_array_t *
ngx_array_create(ngx_pool_t *p, ngx_uint_t n, size_t size)
{
    ngx_array_t *a;

    a = ngx_palloc(p, sizeof(ngx_array_t));
    if (a == NULL) {
        return NULL;
    }

    if (ngx_array_init(a, p, n, size) != NGX_OK) {
        return NULL;
    }

    return a;
}

/*
@brief: 初始化数组。
@param array[inout]: 数组对象。
@param pool[inout]: 内存池对象。
@param n[in]: 数组的元素个数。
@param size[in]: 每个元素的大小。
@return: NGX_OK, NGX_ERROR。
*/
static ngx_inline ngx_int_t
ngx_array_init(ngx_array_t *array, ngx_pool_t *pool, ngx_uint_t n, size_t size)
{
    /*
     * set "array->nelts" before "array->elts", otherwise MSVC thinks
     * that "array->nelts" may be used without having been initialized
     */

    array->nelts = 0;
    array->size = size;
    array->nalloc = n;
    array->pool = pool;

    array->elts = ngx_palloc(pool, n * size);
    if (array->elts == NULL) {
        return NGX_ERROR;
    }

    return NGX_OK;
}
```


### ngx_array_push

```c
/*
@brief: 从数组中获取一个新的节点。
如果数组还没有满，直接一个新的节点就好了。
如果数组满了，1.数组的内存池空间还没满，就直接给数组增加一个元素。
如果数组满了，2.数组的内存池空间也满了，那就给内存池增加一个2倍于现在数组大小的空间。
@param a[inout]: 数组。
@return: 返回一个指向新元素的首地址的指针，如果内存分配失败返回NULL。
*/
void *
ngx_array_push(ngx_array_t *a)
{
    void        *elt, *new;
    size_t       size;
    ngx_pool_t  *p;

    if (a->nelts == a->nalloc) {

        /* the array is full */

        size = a->size * a->nalloc;

        p = a->pool;

        if ((u_char *) a->elts + size == p->d.last
            && p->d.last + a->size <= p->d.end)
        {
            /*
             * the array allocation is the last in the pool
             * and there is space for new allocation
             */

            p->d.last += a->size;
            a->nalloc++;

        } else {
            /* allocate a new array */

            new = ngx_palloc(p, 2 * size);
            if (new == NULL) {
                return NULL;
            }

            ngx_memcpy(new, a->elts, size);
            a->elts = new;
            a->nalloc *= 2;
        }
    }

    elt = (u_char *) a->elts + a->size * a->nelts;
    a->nelts++;

    return elt;
}
```


```c
/*
@brief: 和ngx_array_push一样，只不过从一个元素变成了n个元素。
*/
void *
ngx_array_push_n(ngx_array_t *a, ngx_uint_t n)
{
    void        *elt, *new;
    size_t       size;
    ngx_uint_t   nalloc;
    ngx_pool_t  *p;

    size = n * a->size;

    if (a->nelts + n > a->nalloc) {

        /* the array is full */

        p = a->pool;

        if ((u_char *) a->elts + a->size * a->nalloc == p->d.last
            && p->d.last + size <= p->d.end)
        {
            /*
             * the array allocation is the last in the pool
             * and there is space for new allocation
             */

            p->d.last += size;
            a->nalloc += n;

        } else {
            /* allocate a new array */

            nalloc = 2 * ((n >= a->nalloc) ? n : a->nalloc);

            new = ngx_palloc(p, nalloc * a->size);
            if (new == NULL) {
                return NULL;
            }

            ngx_memcpy(new, a->elts, a->nelts * a->size);
            a->elts = new;
            a->nalloc = nalloc;
        }
    }

    elt = (u_char *) a->elts + a->size * a->nelts;
    a->nelts += n;

    return elt;
}
```




