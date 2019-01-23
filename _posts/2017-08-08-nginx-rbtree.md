---
layout: post
title: "[Nginx] 红黑树"
date: 2017-08-08
author: mdgsf
comments: true
categories: nginx
tags: [Nginx]
description:
published: true #default true
---

源文件如下：

nginx-1.13.1\src\core\ngx_rbtree.h

nginx-1.13.1\src\core\ngx_rbtree.c


## 红黑树原理

<a href="http://blog.csdn.net/yang_yulei/article/details/26066409" target="_blank">http://blog.csdn.net/yang_yulei/article/details/26066409</a>

<a href="http://www.cs.princeton.edu/~rs/talks/LLRB/LLRB.pdf" target="_blank">http://www.cs.princeton.edu/~rs/talks/LLRB/LLRB.pdf</a>

<a href="http://www.cs.princeton.edu/~rs/talks/LLRB/RedBlack.pdf" target="_blank">http://www.cs.princeton.edu/~rs/talks/LLRB/RedBlack.pdf</a>

<a href="https://github.com/julycoding/The-Art-Of-Programming-By-July/blob/master/ebook/zh/03.01.md" target="_blank">https://github.com/julycoding/The-Art-Of-Programming-By-July/blob/master/ebook/zh/03.01.md</a>


<img src="{{ site.url }}/images/2017/08/0802.png" alt="0802" />

## 红黑树的5条性质：

1）每个结点要么是红的，要么是黑的。  

2）根结点是黑的。  

3）每个叶结点（叶结点即指树尾端NIL指针或NULL结点）是黑的。  

4）如果一个结点是红的，那么它的俩个儿子都是黑的。  

5）对于任一结点而言，其到叶结点树尾端NIL指针的每一条路径都包含相同数目的黑结点。 

## 2-3树 和 红黑树

2-3树是完美平衡，就是说从根节点到每一个叶子节点都有相同的高度。

用**2-3树**来理解**红黑树**很简单。

**2节点**: 1个key，2个儿子。

**3节点**: 2个key，3个儿子。

**红色节点**: 相当于3节点。红色表示指向该节点的连接是红色的，红色的连接永远是左儿子。

**黑色节点**: 相当于2节点。黑色表示指向该节点的连接是黑色的，黑色的连接可以是左儿子，也可以是右儿子。
所有叶子节点到根节点的黑色连接数量是一样的。


## 2-3-4树 和 红黑树

不过nginx中的红黑树应该是相当于2-3-4树。

**Perfect balance**. Every path from root to leaf has same length.

**2-node**: one key, two children.

**3-node**: two keys, three children.

**4-node**: three keys, four children.

<img src="{{ site.url }}/images/2017/08/0801_rbtree.png" alt="0801_rbtree" />

在2-3-4树中，3-node转为左斜杠为红色的红黑树。

在2-3-4树中，4-node转为左右斜杠为红色的红黑树。



## 基础数据结构

```c
typedef ngx_uint_t  ngx_rbtree_key_t;
typedef ngx_int_t   ngx_rbtree_key_int_t;


typedef struct ngx_rbtree_node_s  ngx_rbtree_node_t;

struct ngx_rbtree_node_s {
    ngx_rbtree_key_t       key;
    ngx_rbtree_node_t     *left;
    ngx_rbtree_node_t     *right;
    ngx_rbtree_node_t     *parent;
    u_char                 color;
    u_char                 data;
};


typedef struct ngx_rbtree_s  ngx_rbtree_t;

typedef void (*ngx_rbtree_insert_pt) (ngx_rbtree_node_t *root,
    ngx_rbtree_node_t *node, ngx_rbtree_node_t *sentinel);

struct ngx_rbtree_s {
    ngx_rbtree_node_t     *root;
    ngx_rbtree_node_t     *sentinel;
    ngx_rbtree_insert_pt   insert;
};


#define ngx_rbtree_init(tree, s, i)                                           \
    ngx_rbtree_sentinel_init(s);                                              \
    (tree)->root = s;                                                         \
    (tree)->sentinel = s;                                                     \
    (tree)->insert = i



#define ngx_rbt_red(node)               ((node)->color = 1)
#define ngx_rbt_black(node)             ((node)->color = 0)
#define ngx_rbt_is_red(node)            ((node)->color)
#define ngx_rbt_is_black(node)          (!ngx_rbt_is_red(node))
#define ngx_rbt_copy_color(n1, n2)      (n1->color = n2->color)

```


## 插入

<img src="{{ site.url }}/images/2017/08/0801_rbtree.png" alt="0801_rbtree" />

```c
/*
@brief: 红黑树插入操作。
@param tree[inout]: 红黑树。
@param node[in]: 要插入的新节点。
*/
void
ngx_rbtree_insert(ngx_rbtree_t *tree, ngx_rbtree_node_t *node)
{
    ngx_rbtree_node_t  **root, *temp, *sentinel;

    /* a binary tree insert */

    root = &tree->root;
    sentinel = tree->sentinel;

    if (*root == sentinel) {
        node->parent = NULL;
        node->left = sentinel;
        node->right = sentinel;
        ngx_rbt_black(node);
        *root = node;

        return;
    }

    //这里就是普通二叉树的插入操作。
    tree->insert(*root, node, sentinel);

    /* re-balance tree */

    while (node != *root && ngx_rbt_is_red(node->parent)) {

        if (node->parent == node->parent->parent->left) { //上图中只画出了这种情况，else中是左右对称的。
            temp = node->parent->parent->right;

            if (ngx_rbt_is_red(temp)) {
                ngx_rbt_black(node->parent);
                ngx_rbt_black(temp);
                ngx_rbt_red(node->parent->parent);
                node = node->parent->parent;

            } else {
                if (node == node->parent->right) {
                    node = node->parent;
                    ngx_rbtree_left_rotate(root, sentinel, node);
                }

                ngx_rbt_black(node->parent);
                ngx_rbt_red(node->parent->parent);
                ngx_rbtree_right_rotate(root, sentinel, node->parent->parent);
            }

        } else {
            temp = node->parent->parent->left;

            if (ngx_rbt_is_red(temp)) {
                ngx_rbt_black(node->parent);
                ngx_rbt_black(temp);
                ngx_rbt_red(node->parent->parent);
                node = node->parent->parent;

            } else {
                if (node == node->parent->left) {
                    node = node->parent;
                    ngx_rbtree_right_rotate(root, sentinel, node);
                }

                ngx_rbt_black(node->parent);
                ngx_rbt_red(node->parent->parent);
                ngx_rbtree_left_rotate(root, sentinel, node->parent->parent);
            }
        }
    }

    ngx_rbt_black(*root);
}
```



## 遍历

```c
/*
@brief: 返回以node作为树根的这个红黑树中，最小的那个节点。
@param node[in]: 作为树根节点。
@param sentinel[in]: 哨兵节点。
@return: 
*/
static ngx_inline ngx_rbtree_node_t *
ngx_rbtree_min(ngx_rbtree_node_t *node, ngx_rbtree_node_t *sentinel)
{
    while (node->left != sentinel) {
        node = node->left;
    }

    return node;
}

/*
@brief: 返回红黑树tree中节点node的下一个节点。
*/
ngx_rbtree_node_t *
ngx_rbtree_next(ngx_rbtree_t *tree, ngx_rbtree_node_t *node)
{
    ngx_rbtree_node_t  *root, *sentinel, *parent;

    sentinel = tree->sentinel;

    if (node->right != sentinel) {
        return ngx_rbtree_min(node->right, sentinel);
    }

    root = tree->root;

    for ( ;; ) {
        parent = node->parent;

        if (node == root) {
            return NULL;
        }

        if (node == parent->left) {
            return parent;
        }

        node = parent;
    }
}
```

##### 如何遍历整个红黑树？

先调用 ngx_rbtree_min，传入树根，就可以得到该红黑树的最小节点。

然后不断调用 ngx_rbtree_next，就可以遍历整颗红黑树。

## 删除

“在删除结点后，原红黑树的性质可能被改变，如果删除的是红色结点，那么原红黑树的性质依旧保持，此时不用做修正操作，如果删除的结点是黑色结点，原红黑树的性质可能会被改变，我们要对其做修正操作。那么哪些树的性质会发生变化呢，如果删除结点不是树唯一结点，那么删除结点的那一个支的到各叶结点的黑色结点数会发生变化，此时性质5被破坏。如果被删结点的唯一非空子结点是红色，而被删结点的父结点也是红色，那么性质4被破坏。如果被删结点是根结点，而它的唯一非空子结点是红色，则删除后新根结点将变成红色，违背性质2。”


<img src="{{ site.url }}/images/2017/08/0803.png" alt="0803" />




