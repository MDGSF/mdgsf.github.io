---
layout: post
title: "[Nginx] 定时器"
date: 2017-08-09
author: mdgsf
comments: true
categories: nginx
tags: [Nginx]
description:
published: true #default true
---

源文件如下：

nginx-1.13.1\src\event\ngx_event_timer.h

nginx-1.13.1\src\event\ngx_event_timer.c

nginx这里的定时器是用红黑树实现的。

linux中是用时间轮实现的。

我记得还有用最小堆实现定时器的。

## 全局数据变量

```c
#define NGX_TIMER_INFINITE  (ngx_msec_t) -1

#define NGX_TIMER_LAZY_DELAY  300

ngx_rbtree_t              ngx_event_timer_rbtree;

static ngx_rbtree_node_t  ngx_event_timer_sentinel;
```

## ngx_event_timer_init

```c
/*
 * the event timer rbtree may contain the duplicate keys, however,
 * it should not be a problem, because we use the rbtree to find
 * a minimum timer value only
 */

/*
@brief: 定时器初始化，其实就是初始化一颗红黑树。
*/
ngx_int_t
ngx_event_timer_init(ngx_log_t *log)
{
    ngx_rbtree_init(&ngx_event_timer_rbtree, &ngx_event_timer_sentinel,
                    ngx_rbtree_insert_timer_value);

    return NGX_OK;
}

/*
@brief: 红黑树中的插入函数。
@param temp: 临时指针，指向红黑树中的节点。
@param node: 要插入的新节点。
@param sentinel: 哨兵节点。
*/
void
ngx_rbtree_insert_timer_value(ngx_rbtree_node_t *temp, ngx_rbtree_node_t *node,
    ngx_rbtree_node_t *sentinel)
{
    ngx_rbtree_node_t  **p;

    for ( ;; ) {

        /*
         * Timer values
         * 1) are spread in small range, usually several minutes,
         * 2) and overflow each 49 days, if milliseconds are stored in 32 bits.
         * The comparison takes into account that overflow.
         */

        /*  node->key < temp->key */

        p = ((ngx_rbtree_key_int_t) (node->key - temp->key) < 0)
            ? &temp->left : &temp->right;

        if (*p == sentinel) {
            break;
        }

        temp = *p;
    }

    *p = node;
    node->parent = temp;
    node->left = sentinel;
    node->right = sentinel;
    ngx_rbt_red(node);
}
```

## ngx_event_add_timer

```c
/*
@brief: 把时间ev添加到定时器中去，超时时间是timer，单位应该是毫秒。
可以看到这里定时器和事件是一起使用的。
@param ev: 事件。
@param timer: 超时时间，比如说100ms，就表示100ms后定时器促发，就会执行这个事件。
@tips:
//ngx_current_msec在源文件 nginx-1.13.1\src\core\ngx_times.c
volatile ngx_msec_t      ngx_current_msec;
我猜这个应该就是当前时间的意思。
*/
static ngx_inline void
ngx_event_add_timer(ngx_event_t *ev, ngx_msec_t timer)
{
    ngx_msec_t      key;
    ngx_msec_int_t  diff;

    //ngx_current_msec:当前时间， timer:超时时间， key:定时器促发的时间。
    key = ngx_current_msec + timer;

    if (ev->timer_set) {
        //如果定时器被设置过了

        /*
         * Use a previous timer value if difference between it and a new
         * value is less than NGX_TIMER_LAZY_DELAY milliseconds: this allows
         * to minimize the rbtree operations for fast connections.
         */

        diff = (ngx_msec_int_t) (key - ev->timer.key);

        if (ngx_abs(diff) < NGX_TIMER_LAZY_DELAY) { //如果之前的定时器的促发时间和现在的相距很近，那就用之前的定时器。
            ngx_log_debug3(NGX_LOG_DEBUG_EVENT, ev->log, 0,
                           "event timer: %d, old: %M, new: %M",
                            ngx_event_ident(ev->data), ev->timer.key, key);
            return;
        }

        ngx_del_timer(ev); //不然的话，先把之前的定时器删除掉。
    }

    ev->timer.key = key;

    ngx_log_debug3(NGX_LOG_DEBUG_EVENT, ev->log, 0,
                   "event timer add: %d: %M:%M",
                    ngx_event_ident(ev->data), timer, ev->timer.key);

    ngx_rbtree_insert(&ngx_event_timer_rbtree, &ev->timer); //把新的定时器插入红黑树中。

    ev->timer_set = 1;
}
```

## ngx_event_del_timer

```c
/*
@brief: 把事件ev从定时器中删除。
@param ev: 事件。
*/
static ngx_inline void
ngx_event_del_timer(ngx_event_t *ev)
{
    ngx_log_debug2(NGX_LOG_DEBUG_EVENT, ev->log, 0,
                   "event timer del: %d: %M",
                    ngx_event_ident(ev->data), ev->timer.key);

    ngx_rbtree_delete(&ngx_event_timer_rbtree, &ev->timer); //可以看到，其实就只是删除红黑树中的一个节点。

#if (NGX_DEBUG)
    ev->timer.left = NULL;
    ev->timer.right = NULL;
    ev->timer.parent = NULL;
#endif

    ev->timer_set = 0;
}
```

## ngx_event_find_timer

```c
/*
@brief:
如果没有一个定时器，那就返回NGX_TIMER_INFINITE。
如果最近的一个定时器还没有超时，那就返回这个定时器还有多久促发。
否则返回0.
*/
ngx_msec_t
ngx_event_find_timer(void)
{
    ngx_msec_int_t      timer;
    ngx_rbtree_node_t  *node, *root, *sentinel;

    if (ngx_event_timer_rbtree.root == &ngx_event_timer_sentinel) {
        return NGX_TIMER_INFINITE;
    }

    root = ngx_event_timer_rbtree.root;
    sentinel = ngx_event_timer_rbtree.sentinel;

    node = ngx_rbtree_min(root, sentinel);

    timer = (ngx_msec_int_t) (node->key - ngx_current_msec);

    return (ngx_msec_t) (timer > 0 ? timer : 0);
}
```

## ngx_event_expire_timers

```c
/*
@brief: 执行所有已经超时的定时器。
*/
void
ngx_event_expire_timers(void)
{
    ngx_event_t        *ev;
    ngx_rbtree_node_t  *node, *root, *sentinel;

    sentinel = ngx_event_timer_rbtree.sentinel;

    for ( ;; ) {
        root = ngx_event_timer_rbtree.root;

        if (root == sentinel) { //如果没有定时器
            return;
        }

        node = ngx_rbtree_min(root, sentinel);

        /* node->key > ngx_current_msec */

        if ((ngx_msec_int_t) (node->key - ngx_current_msec) > 0) { //如果最近的定时器都还没有超时，那就结束。
            return;
        }

        //通过node获取到对应的事件结构体的指针。
        ev = (ngx_event_t *) ((char *) node - offsetof(ngx_event_t, timer));

        ngx_log_debug2(NGX_LOG_DEBUG_EVENT, ev->log, 0,
                       "event timer del: %d: %M",
                       ngx_event_ident(ev->data), ev->timer.key);

        ngx_rbtree_delete(&ngx_event_timer_rbtree, &ev->timer); //先将该定时器删除。

#if (NGX_DEBUG)
        ev->timer.left = NULL;
        ev->timer.right = NULL;
        ev->timer.parent = NULL;
#endif

        ev->timer_set = 0;

        ev->timedout = 1;

        ev->handler(ev); //促发该事件。
    }
}
```

## ngx_event_no_timers_left

```c
/*
@brief:
*/
ngx_int_t
ngx_event_no_timers_left(void)
{
    ngx_event_t        *ev;
    ngx_rbtree_node_t  *node, *root, *sentinel;

    sentinel = ngx_event_timer_rbtree.sentinel;
    root = ngx_event_timer_rbtree.root;

    if (root == sentinel) {
        return NGX_OK;
    }

    for (node = ngx_rbtree_min(root, sentinel);
         node;
         node = ngx_rbtree_next(&ngx_event_timer_rbtree, node))
    {
        ev = (ngx_event_t *) ((char *) node - offsetof(ngx_event_t, timer));

        if (!ev->cancelable) { //不知道cancelable这个代表什么意思。
            return NGX_AGAIN;
        }
    }

    /* only cancelable timers left */

    return NGX_OK;
}
```
