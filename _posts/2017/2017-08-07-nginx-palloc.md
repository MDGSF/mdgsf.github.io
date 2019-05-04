---
layout: post
title: "[Nginx] palloc 内存分配"
date: 2017-08-07
author: mdgsf
comments: true
categories: nginx
tags: [Nginx]
description:
published: true #default true
---

### 用到的结构体定义

```c
/*
 * NGX_MAX_ALLOC_FROM_POOL should be (ngx_pagesize - 1), i.e. 4095 on x86.
 * On Windows NT it decreases a number of locked pages in a kernel.
 */
#define NGX_MAX_ALLOC_FROM_POOL  (ngx_pagesize - 1)

#define NGX_DEFAULT_POOL_SIZE    (16 * 1024)

#define NGX_POOL_ALIGNMENT       16
#define NGX_MIN_POOL_SIZE                                                     \
    ngx_align((sizeof(ngx_pool_t) + 2 * sizeof(ngx_pool_large_t)),            \
              NGX_POOL_ALIGNMENT)


typedef void (*ngx_pool_cleanup_pt)(void *data);

typedef struct ngx_pool_cleanup_s  ngx_pool_cleanup_t;

struct ngx_pool_cleanup_s {
    ngx_pool_cleanup_pt   handler;
    void                 *data;
    ngx_pool_cleanup_t   *next;
};


typedef struct ngx_pool_large_s  ngx_pool_large_t;

struct ngx_pool_large_s {
    ngx_pool_large_t     *next;
    void                 *alloc;
};


typedef struct {
    u_char               *last;
    u_char               *end;
    ngx_pool_t           *next;
    ngx_uint_t            failed;
} ngx_pool_data_t;


struct ngx_pool_s {
    ngx_pool_data_t       d;
    size_t                max;
    ngx_pool_t           *current;
    ngx_chain_t          *chain;
    ngx_pool_large_t     *large;
    ngx_pool_cleanup_t   *cleanup;
    ngx_log_t            *log;
};


typedef struct {
    ngx_fd_t              fd;
    u_char               *name;
    ngx_log_t            *log;
} ngx_pool_cleanup_file_t;
```

1. 先不管这些结构体什么意思。

2. ngx_log_t 是用来输出日志的，直接忽略它的存在。

### ngx_align 内存对齐

```c
#define ngx_align(d, a)     (((d) + (a - 1)) & ~(a - 1))
#define ngx_align_ptr(p, a)                                                   \
    (u_char *) (((uintptr_t) (p) + ((uintptr_t) a - 1)) & ~((uintptr_t) a - 1))
```

### ngx_alloc

就只是对malloc的调用。

```c
/*
@brief: 分配一块内存空间。
@param size[in]: 要分配的内存空间大小，单位：字节。
@param log: 日志输出。
@return : 返回值是指向分配好的内存空间首地址。
*/
void *
ngx_alloc(size_t size, ngx_log_t *log)
{
    void  *p;

    p = malloc(size);
    if (p == NULL) {
        ngx_log_error(NGX_LOG_EMERG, log, ngx_errno,
                      "malloc(%uz) failed", size);
    }

    ngx_log_debug2(NGX_LOG_DEBUG_ALLOC, log, 0, "malloc: %p:%uz", p, size);

    return p;
}
```

### ngx_calloc

```c
#define ngx_memzero(buf, n)       (void) memset(buf, 0, n)

/*
@brief: 分配一块内存空间，并把分配好的空间清零。
@param size[in]: 要分配的内存空间大小，单位：字节。
@param log: 日志输出。
@return : 返回值是指向分配好的内存空间首地址。
*/
void *
ngx_calloc(size_t size, ngx_log_t *log)
{
    void  *p;

    p = ngx_alloc(size, log);

    if (p) {
        ngx_memzero(p, size);
    }

    return p;
}
```

### ngx_create_pool

```c
/*
@brief: 创建一个内存池对象。
@param size[in]: 要分配的内存大小。
@param log: 日志输出。
@return: 返回一个内存池对象的指针，失败返回NULL。
*/
ngx_pool_t *
ngx_create_pool(size_t size, ngx_log_t *log)
{
    ngx_pool_t  *p;

    //申请一块size大小的内存空间，以NGX_POOL_ALIGNMENT=16对齐。
    p = ngx_memalign(NGX_POOL_ALIGNMENT, size, log);
    if (p == NULL) {
        return NULL;
    }

    p->d.last = (u_char *) p + sizeof(ngx_pool_t);
    p->d.end = (u_char *) p + size;
    p->d.next = NULL;
    p->d.failed = 0;

    //这里p->max最大只能为NGX_MAX_ALLOC_FROM_POOL=4096 on x86
    size = size - sizeof(ngx_pool_t);
    p->max = (size < NGX_MAX_ALLOC_FROM_POOL) ? size : NGX_MAX_ALLOC_FROM_POOL;

    p->current = p;
    p->chain = NULL;
    p->large = NULL;
    p->cleanup = NULL;
    p->log = log;

    return p;
}
```

需要说明的是size的选择，size的大小必须小于等于NGX_MAX_ALLOC_FROM_POOL，且必须大于sizeof(ngx_pool_t)。

选择大于NGX_MAX_ALLOC_FROM_POOL的值会造成浪费，因为大于该限制的空间不会被用到。

选择小于sizeof(ngx_pool_t)的值会造成程序崩溃。由于初始大小的内存块中要用一部分来存储ngx_pool_t这个信息本身。

### ngx_memalign

<a href="http://mdgsf.github.io/linux/2017/08/07/linux-memalign.html" target="_blank">http://mdgsf.github.io/linux/2017/08/07/linux-memalign.html</a>

```c
/*
@brief: 如果memalign() 或者 posix_memalign() 可以只用，那就用这两个函数申请一块对齐的内存空间;
否则的话，就调用malloc()申请一块空间。
*/

/*
 * Linux has memalign() or posix_memalign()
 * Solaris has memalign()
 * FreeBSD 7.0 has posix_memalign(), besides, early version's malloc()
 * aligns allocations bigger than page size at the page boundary
 */

#if (NGX_HAVE_POSIX_MEMALIGN || NGX_HAVE_MEMALIGN)

void *ngx_memalign(size_t alignment, size_t size, ngx_log_t *log);

#else

#define ngx_memalign(alignment, size, log)  ngx_alloc(size, log)

#endif


/////////////////////////////////////////////////////////////////

#if (NGX_HAVE_POSIX_MEMALIGN)

void *
ngx_memalign(size_t alignment, size_t size, ngx_log_t *log)
{
    void  *p;
    int    err;

    err = posix_memalign(&p, alignment, size);

    if (err) {
        ngx_log_error(NGX_LOG_EMERG, log, err,
                      "posix_memalign(%uz, %uz) failed", alignment, size);
        p = NULL;
    }

    ngx_log_debug3(NGX_LOG_DEBUG_ALLOC, log, 0,
                   "posix_memalign: %p:%uz @%uz", p, size, alignment);

    return p;
}

#elif (NGX_HAVE_MEMALIGN)

void *
ngx_memalign(size_t alignment, size_t size, ngx_log_t *log)
{
    void  *p;

    p = memalign(alignment, size);
    if (p == NULL) {
        ngx_log_error(NGX_LOG_EMERG, log, ngx_errno,
                      "memalign(%uz, %uz) failed", alignment, size);
    }

    ngx_log_debug3(NGX_LOG_DEBUG_ALLOC, log, 0,
                   "memalign: %p:%uz @%uz", p, size, alignment);

    return p;
}

#endif

```

### ngx_palloc 和 ngx_pnalloc

```c
/*
@brief:从内存池pool中分配一块大小为size的内存，此函数分配的内存的起始地址按照NGX_ALIGNMENT进行了对齐。对齐操作会提高系统处理的速度，但会造成少量内存的浪费。
@param pool[in]: 内存池pool。
@param size[in]: 要分配的内存大小。
@return: 返回执行分配的内存空间的首地址。
*/
void *
ngx_palloc(ngx_pool_t *pool, size_t size)
{
#if !(NGX_DEBUG_PALLOC)
    if (size <= pool->max) {
        return ngx_palloc_small(pool, size, 1);
    }
#endif

    return ngx_palloc_large(pool, size);
}

/*
@brief:从内存池pool中分配一块大小为size的内存，没有进行内存对齐。
@param pool[in]: 内存池pool。
@param size[in]: 要分配的内存大小。
@return: 返回执行分配的内存空间的首地址。
*/
void *
ngx_pnalloc(ngx_pool_t *pool, size_t size)
{
#if !(NGX_DEBUG_PALLOC)
    if (size <= pool->max) {
        return ngx_palloc_small(pool, size, 0);
    }
#endif

    return ngx_palloc_large(pool, size);
}
```

可以看到，当size <= pool->max 时，调用ngx_palloc_small()。否则调用ngx_palloc_large()。

### ngx_palloc_small

```c
/*
@brief:从内存池pool中分配一块大小为size的内存。
@param pool[in]: 内存池pool。
@param size[in]: 要分配的内存大小。
@param align[in]: 1:内存对齐， 0:内存没有对齐。
@return: 返回执行分配的内存空间的首地址。
*/
static ngx_inline void *
ngx_palloc_small(ngx_pool_t *pool, size_t size, ngx_uint_t align)
{
    u_char      *m;
    ngx_pool_t  *p;

    p = pool->current;

    do {
        m = p->d.last;

        if (align) {
            m = ngx_align_ptr(m, NGX_ALIGNMENT); //对齐内存指针。
        }

        if ((size_t) (p->d.end - m) >= size) { //如果当前的ngx_pool_t对象中的内存空间足够，那就直接返回。
            p->d.last = m + size;

            return m;
        }

        p = p->d.next; //到下一个ngx_pool_t对象中去查找，这是一个链表结构。

    } while (p);

    return ngx_palloc_block(pool, size);
}

/*
@brief: 这个函数做了两件事：
1. 为ngx_pool_t对象分配一个新的内存节点，新的节点的大小和pool一样大，链表结构， pool.d.next = new ngx_pool_t。
2. 然后从这个新的节点上分配出size大小的内存来使用。
@param pool[inout]: 要增加新的节点的内存池。
@param size[in]: 要分配的内存大小。
@return: 返回size大小的内存空间的首地址。
*/
static void *
ngx_palloc_block(ngx_pool_t *pool, size_t size)
{
    u_char      *m;
    size_t       psize;
    ngx_pool_t  *p, *new;

    psize = (size_t) (pool->d.end - (u_char *) pool); //获取pool的大小

    m = ngx_memalign(NGX_POOL_ALIGNMENT, psize, pool->log); //分配一块新的节点
    if (m == NULL) {
        return NULL;
    }

    new = (ngx_pool_t *) m;

    new->d.end = m + psize;
    new->d.next = NULL;
    new->d.failed = 0;

    m += sizeof(ngx_pool_data_t); //这里做了优化，新的节点只使用了ngx_pool_data_t
    m = ngx_align_ptr(m, NGX_ALIGNMENT);
    new->d.last = m + size;

    for (p = pool->current; p->d.next; p = p->d.next) {
        if (p->d.failed++ > 4) {
            pool->current = p->d.next;
        }
    }

    p->d.next = new; //把新的节点链接到链表上去。

    return m;
}
```

### ngx_palloc_large

```c
/*
@brief: 分配一块size大小的内存，并把该内存链接到pool->large这个链表上去，在size > pool->max的时候才会调用这个函数。
@param pool[in]: 内存池对象。
@param size[in]: 要分配的内存空间大小。
@return: 返回指向改内存空间的首地址的指针。
*/
static void *
ngx_palloc_large(ngx_pool_t *pool, size_t size)
{
    void              *p;
    ngx_uint_t         n;
    ngx_pool_large_t  *large;

    p = ngx_alloc(size, pool->log);
    if (p == NULL) {
        return NULL;
    }

    n = 0;

    for (large = pool->large; large; large = large->next) {
        if (large->alloc == NULL) {
            large->alloc = p;
            return p;
        }

        if (n++ > 3) {
            break;
        }
    }

    large = ngx_palloc_small(pool, sizeof(ngx_pool_large_t), 1);
    if (large == NULL) {
        ngx_free(p);
        return NULL;
    }

    large->alloc = p;
    large->next = pool->large;
    pool->large = large;

    return p;
}
```

### ngx_pmemalign

```c
/*
@brief: 以内存对齐的方式，申请一块空间，并放在pool->large链表上。
@param pool[inout]: 内存池对象。
@param size[in]: 要分配的空间大小。
@param alignment[in]: 对齐的字节数，必须是2的倍数。
@return: 返回分配的内存空间的首地址的指针。
*/
void *
ngx_pmemalign(ngx_pool_t *pool, size_t size, size_t alignment)
{
    void              *p;
    ngx_pool_large_t  *large;

    p = ngx_memalign(alignment, size, pool->log);
    if (p == NULL) {
        return NULL;
    }

    large = ngx_palloc_small(pool, sizeof(ngx_pool_large_t), 1);
    if (large == NULL) {
        ngx_free(p);
        return NULL;
    }

    large->alloc = p;
    large->next = pool->large;
    pool->large = large;

    return p;
}
```

### ngx_pfree

```c
/*
@brief: 从pool->large链表中查找是否存在首地址为p的内存空间，如果存在则释放这块空间。
@param pool[inout]: 内存池对象。
@param p[in]: 要释放的内存空间。
@return:
NGX_OK: 释放成功。
NGX_DECLINED: 没有找到。
*/
ngx_int_t
ngx_pfree(ngx_pool_t *pool, void *p)
{
    ngx_pool_large_t  *l;

    for (l = pool->large; l; l = l->next) {
        if (p == l->alloc) {
            ngx_log_debug1(NGX_LOG_DEBUG_ALLOC, pool->log, 0,
                           "free: %p", l->alloc);
            ngx_free(l->alloc);
            l->alloc = NULL;

            return NGX_OK;
        }
    }

    return NGX_DECLINED;
}
```

对于被置于大块内存链，也就是被large字段管理的一列内存中的某块进行释放。该函数的实现是顺序遍历large管理的大块内存链表。所以效率比较低下。如果在这个链表中找到了这块内存，则释放，并返回NGX_OK。否则返回NGX_DECLINED。

由于这个操作效率比较低下，除非必要，也就是说这块内存非常大，确应及时释放，否则一般不需要调用。反正内存在这个pool被销毁的时候，总归会都释放掉的嘛！

### 参考链接

<a href="http://tengine.taobao.org/book/chapter_02.html#ngx-pool-t-100" target="_blank">http://tengine.taobao.org/book/chapter_02.html#ngx-pool-t-100</a>
