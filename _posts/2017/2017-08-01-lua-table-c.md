---
layout: post
title: "[Lua] Table内部实现"
date: 2017-08-01
author: mdgsf
comments: true
categories: lua
tags: [Lua]
description:
published: true #default true
---

## Table 结构定义

Lua 的Table是由数组、哈希表一起实现的。

所以如果不清楚哈希表，建议先看看哈希表。
<a href="http://mdgsf.github.io/c/2016/07/01/c-hashtable.html" target="_blank">http://mdgsf.github.io/c/2016/07/01/c-hashtable.html</a>

```c
typedef unsigned char lu_byte;

typedef struct Table {
  CommonHeader;
  lu_byte flags;  /* 1<<p means tagmethod(p) is not present */
  lu_byte lsizenode;  /* log2 of size of 'node' array */
  unsigned int sizearray;  /* size of 'array' array */
  TValue *array;  /* array part */
  Node *node;
  Node *lastfree;  /* any free position is before this position */
  struct Table *metatable;
  GCObject *gclist;
} Table;
```

TValue *array; 是数组

unsigned int sizearray; 是数组的大小

Node *node; 应该就是哈希表了。

lu_byte lsizenode; 如果哈希表的容量为Size，那么lsizenode = log2(Size)

其他的成员变量暂时不关心。 等等主要看下 TValue 和 Node 的结构体。

```c
Table *luaH_new (lua_State *L) {
  GCObject *o = luaC_newobj(L, LUA_TTABLE, sizeof(Table)); //其实就是分配一款内存空间
  Table *t = gco2t(o); //把指针类型转换为Table类型。
  t->metatable = NULL; //接下来的都是一些初始化的操作。
  t->flags = cast_byte(~0);
  t->array = NULL;
  t->sizearray = 0;
  setnodevector(L, t, 0);
  return t;
}

static void setnodevector (lua_State *L, Table *t, unsigned int size) {
  if (size == 0) {  /* no elements to hash part? */
    t->node = cast(Node *, dummynode);  /* use common 'dummynode' */
    t->lsizenode = 0;
    t->lastfree = NULL;  /* signal that it is using dummy node */
  }
  else {
      ... //这里我们暂时不关心，就先不看。
  }
}
```

```c
#define cast(t, exp) ((t)(exp))   //类型转换，把表达式exp转换为t类型。

#define twoto(x) (1<<(x)) //计算2^x

#define sizenode(t) (twoto((t)->lsizenode)) //计算2^(t->lsizenode)的大小

void luaH_free (lua_State *L, Table *t) {
  if (!isdummy(t))
    luaM_freearray(L, t->node, cast(size_t, sizenode(t))); //释放哈希表的空间，大小为2^(t->lsizenode)
  luaM_freearray(L, t->array, t->sizearray); //释放数组的空间，大小为t->sizearray
  luaM_free(L, t); //释放Table的内存空间
}
```

## Node TKey TValue

可以看到，数组是用TValue来保存值的，而哈希表是使用Node来保存值。

TValue *array; 是数组

Node *node; 应该就是哈希表了。

```c
typedef struct Node {
  TValue i_val;
  TKey i_key;
} Node;
```

```c
typedef union TKey {
  struct {
    TValuefields;
    int next;  /* for chaining (offset for next node) */
  } nk;
  TValue tvk;
} TKey;
```

```c
/*
** Tagged Values. This is the basic representation of values in Lua,
** an actual value plus a tag with its type.
*/

/*
** Union of all Lua values
*/
typedef union Value {
  GCObject *gc;    /* collectable objects */
  void *p;         /* light userdata */
  int b;           /* booleans */
  lua_CFunction f; /* light C functions */
  lua_Integer i;   /* integer numbers */
  lua_Number n;    /* float numbers */
} Value;

#define TValuefields Value value_; int tt_

typedef struct lua_TValue {
  TValuefields;
} TValue;
```

其实 TKey 和 TValue 是同一个东西，只不过TKey里面多了一个next，主要是哈希表出现冲突时，用来解决冲突用的。

TKey中tvk是这个key的值，nk中的next则指向key冲突的下一个节点。lua的hash表的hash算法比较特别，一般的hash表都是根据key算出hash(key)，然后把这个key放在hash表的hash(key)位置上，如果有冲突的话，就放在hash(key)位置的链表上。

但是lua的hash表中，如果有冲突的话，lua会找hash表中一个空的位置（从后往前找，假设为x），然后把新的key放在这个空的位置x上，并且让hash表中hash(key)处的节点的nk.next指向x。这个意思就是，假如有冲突的话，不用重新分配空间来存储冲突的key，而是利用hash表上未用过的空格来存储。但是这样会引入另外一个问题，本来key是不应该放在x的，假设有另外一个key2，hash(key2)算出来的位置也在x的话，那就表示本来x这个位置应该是给key2的，但是由于x被key占用了，导致key2没地方放了。这时候lua的处理方式是把key放到另外一个空格，然后让key2占回x。当hash表已经没有空格的时候，lua就会resize这个hash表。这样做的好处主要是不用动态申请内存空间，hash表初始化的时候有多少内存空间就用多少，不够就resize这个hash表。

<a href="http://blog.csdn.net/u012611878/article/details/51873648" target="_blank">http://blog.csdn.net/u012611878/article/details/51873648</a>

冲突解决技术可以分为两类：**开散列方法**( open hashing，也称为拉链法，separate chaining )和**闭散列方法**( closed hashing，也称为开地址方法，open addressing )。这两种方法的不同之处在于：开散列法把发生冲突的关键码存储在散列表主表之外，而闭散列法把发生冲突的关键码存储在表中另一个槽内。

Lua这里使用的是闭散列。

## 增删查改

```c
LUAI_DDEC const TValue luaO_nilobject_;
#define luaO_nilobject (&luaO_nilobject_) //这是一个空对象，这样的好处是可以不需要处理NULL指针，在<<代码整洁之道>>里面有说。

TValue *luaH_set (lua_State *L, Table *t, const TValue *key) {
  const TValue *p = luaH_get(t, key); //应该是到Table中去查找key是不是存在。
  if (p != luaO_nilobject)
    return cast(TValue *, p); //如果存在，直接返回对应的TValue
  else return luaH_newkey(L, t, key); //如果不存在，则创建一个新的TValue，再返回。
}
```

很明显，上面有两个重要的函数 luaH_newkey 和 luaH_get。

luaH_newkey 应该就是插入函数了，向Table内插入一个新的值。

luaH_get 则是查找函数，在Table中查找对应的key是不是存在。

### luaH_newkey

```c
/*
** inserts a new key into a hash table; first, check whether key's main
** position is free. If not, check whether colliding node is in its main
** position or not: if it is not, move colliding node to an empty place and
** put new key in its main position; otherwise (colliding node is in its main
** position), new key goes to an empty position.
*/
/*
插入一个新的key到Table中。首先，检查key的main position是不是空的，main position 就是key要插入的位置吧。
if(main position != 空)
{
    检查冲突节点的main position是不是在它自己的位置上。
    if(冲突节点不是在它自己的位置上)
    {
        把冲突节点移动到一个空的位置上。
        把新的key放在它自己的main position上。
    }
    else
    {
        把新的key放到一个空的位置上。
    }
}
else //main position == 空
{
    把新的key放在它自己的main position上。
}
*/
TValue *luaH_newkey (lua_State *L, Table *t, const TValue *key) {
  Node *mp;
  TValue aux;
  if (ttisnil(key)) luaG_runerror(L, "table index is nil");
  else if (ttisfloat(key)) {
    lua_Integer k;
    if (luaV_tointeger(key, &k, 0)) {  /* does index fit in an integer? */
      setivalue(&aux, k);
      key = &aux;  /* insert it as an integer */
    }
    else if (luai_numisnan(fltvalue(key)))
      luaG_runerror(L, "table index is NaN");
  }
  mp = mainposition(t, key);  ------------------------------------------//通过key可以获取到Table中的一个位置mp，哈希函数就在mainposition()这函数里面。
  if (!ttisnil(gval(mp)) || isdummy(t)) {  /* main position is taken? */
    Node *othern;
    Node *f = getfreepos(t);  /* get a free place */
    if (f == NULL) {  /* cannot find a free place? */
      rehash(L, t, key);  /* grow table */    ----------------------------//空间不足了，扩大空间。
      /* whatever called 'newkey' takes care of TM cache */
      return luaH_set(L, t, key);  /* insert key into grown table */
    }
    lua_assert(!isdummy(t));
    othern = mainposition(t, gkey(mp));  ---------------------------------//获取冲突节点的main position。
    if (othern != mp) {  /* is colliding node out of its main position? */
      /* yes; move colliding node into free position */
      while (othern + gnext(othern) != mp)  /* find previous */
        othern += gnext(othern);
      gnext(othern) = cast_int(f - othern);  /* rechain to point to 'f' */
      *f = *mp;  /* copy colliding node into free pos. (mp->next also goes) */
      if (gnext(mp) != 0) {
        gnext(f) += cast_int(mp - f);  /* correct 'next' */
        gnext(mp) = 0;  /* now 'mp' is free */
      }
      setnilvalue(gval(mp));
    }
    else {  /* colliding node is in its own main position */
      /* new node will go into free position */
      if (gnext(mp) != 0)
        gnext(f) = cast_int((mp + gnext(mp)) - f);  /* chain new position */
      else lua_assert(gnext(f) == 0);
      gnext(mp) = cast_int(f - mp);  -------------------------------------//发生冲突时，next保存的是两个指针的差值，也就是当前节点到下一个节点在内存中的距离。
      mp = f;
    }
  }
  setnodekey(L, &mp->i_key, key);
  luaC_barrierback(L, t, key);
  lua_assert(ttisnil(gval(mp)));
  return gval(mp);
}

//这里是上面用到的一些宏
#define gnode(t,i) (&(t)->node[i])
#define gnext(n) ((n)->i_key.nk.next)
#define gkey(n) cast(const TValue*, (&(n)->i_key.tvk))  //获取Node的key
#define gval(n) (&(n)->i_val)  //获取Node的value

/* type casts (a macro highlights casts in the code) */
#define cast(t, exp) ((t)(exp))
#define cast_int(i) cast(int, (i)) //转换为整形

#define isdummy(t) ((t)->lastfree == NULL)

#define LUA_TNIL 0
#define rttype(o) ((o)->tt_)
#define checktag(o,t) (rttype(o) == (t))
#define ttisnil(o) checktag((o), LUA_TNIL)
```

在这个函数里面，用到了3个函数：getfreepos，mainposition，rehash。我们需要看看到底是怎么实现的。

getfreepos: 获取一个空的位置。

rehash: 重新分配Table的内存空间。

mainposition: 根据key值，通过哈希函数算出在Table中的main position。

### getfreepos

```c
static Node *getfreepos (Table *t) {
  if (!isdummy(t)) {
    while (t->lastfree > t->node) {
      t->lastfree--; //这里再不断的向前遍历
      if (ttisnil(gkey(t->lastfree)))
        return t->lastfree;
    }
  }
  return NULL;  /* could not find a free place */
}
```

看来Table 中的 lastfree 这个变量是用来保存最后一个空闲位置的指针。

### rehash

```c
/*
** nums[i] = number of keys 'k' where 2^(i - 1) < k <= 2^i
*/
/*
na: Table中数组中的元素个数 和 哈希表中可以放到数组中去的元素的个数 的总和。
totaluse: Table中所有元素的个数，包括数组和哈希表。
*/
static void rehash (lua_State *L, Table *t, const TValue *ek) {
  unsigned int asize;  /* optimal size for array part */
  unsigned int na;  /* number of keys in the array part */
  unsigned int nums[MAXABITS + 1];
  int i;
  int totaluse;
  for (i = 0; i <= MAXABITS; i++) nums[i] = 0;  /* reset counts */
  na = numusearray(t, nums);  /* count keys in array part */
  totaluse = na;  /* all those keys are integer keys */
  totaluse += numusehash(t, nums, &na);  /* count keys in hash part */
  /* count extra key */
  na += countint(ek, nums);
  totaluse++;
  /* compute new size for array part */
  asize = computesizes(nums, &na);
  /* resize the table to new computed sizes */
  luaH_resize(L, t, asize, totaluse - na);
}
```

```c
/*
** Count keys in array part of table 't': Fill 'nums[i]' with
** number of keys that will go into corresponding slice and return
** total number of non-nil keys.
*/
/*
@brief 这个函数是用来计算Table中的数组部分的信息的。
@param t[in]: Table。
@param nums[out]: 这个数组的大小是32。
    nums[0]: array[0]
    nums[1]: array[1 - 2]
    nums[2]: array[2 - 4]
    nums[3]: array[4 - 8]
    ...
    nums[i]: array[2^(i-1) - 2^i]
    nums[i]保存着数组下标范围从 2^(i-1) 到 2^i 之间的所有非空元素的个数。
@return: 返回值是数组中所有的非空元素的个数。
*/
static unsigned int numusearray (const Table *t, unsigned int *nums) {
  int lg;
  unsigned int ttlg;  /* 2^lg */
  unsigned int ause = 0;  /* summation of 'nums' */
  unsigned int i = 1;  /* count to traverse all array keys */
  /* traverse each slice */
  for (lg = 0, ttlg = 1; lg <= MAXABITS; lg++, ttlg *= 2) {
    unsigned int lc = 0;  /* counter */
    unsigned int lim = ttlg;
    if (lim > t->sizearray) {
      lim = t->sizearray;  /* adjust upper limit */
      if (i > lim)
        break;  /* no more elements to count */
    }
    /* count elements in range (2^(lg - 1), 2^lg] */
    for (; i <= lim; i++) {
      if (!ttisnil(&t->array[i-1]))
        lc++;
    }
    nums[lg] += lc;
    ause += lc;
  }
  return ause;
}
```

```c
/*
@brief: 计算Table中哈希表的信息。
@param t[in]: Table。
@param nums[inout]:
@pram pna[inout]: 就是现在在哈希表中，但是可以移动到数组中去的元素的个数。
@return: 返回哈希表中所有元素的个数。
*/
static int numusehash (const Table *t, unsigned int *nums, unsigned int *pna) {
  int totaluse = 0;  /* total number of elements */
  int ause = 0;  /* elements added to 'nums' (can go to array part) */
  int i = sizenode(t);
  while (i--) {
    Node *n = &t->node[i];
    if (!ttisnil(gval(n))) {
      ause += countint(gkey(n), nums);
      totaluse++;
    }
  }
  *pna += ause;
  return totaluse;
}
```

```c
/*
** Compute the optimal size for the array part of table 't'. 'nums' is a
** "count array" where 'nums[i]' is the number of integers in the table
** between 2^(i - 1) + 1 and 2^i. 'pna' enters with the total number of
** integer keys in the table and leaves with the number of keys that
** will go to the array part; return the optimal size.
*/
static unsigned int computesizes (unsigned int nums[], unsigned int *pna) {
  int i;
  unsigned int twotoi;  /* 2^i (candidate for optimal size) */
  unsigned int a = 0;  /* number of elements smaller than 2^i */
  unsigned int na = 0;  /* number of elements to go to array part */
  unsigned int optimal = 0;  /* optimal size for array part */
  /* loop while keys can fill more than half of total size */
  for (i = 0, twotoi = 1; *pna > twotoi / 2; i++, twotoi *= 2) {
    if (nums[i] > 0) {
      a += nums[i];
      if (a > twotoi/2) {  /* more than half elements present? */
        optimal = twotoi;  /* optimal size (till now) */
        na = a;  /* all elements up to 'optimal' will go to array part */
      }
    }
  }
  lua_assert((optimal == 0 || optimal / 2 < na) && na <= optimal);
  *pna = na;
  return optimal;
}
```

对于存放在数组有一个规则，每插入一个整数key时，都要判断包含当前key的区间[1, 2^n]里，是否满足table里所有整数类型key的数量大于2^(n - 1)，如果不成立则需要把这个key放在hash表里。这样设计，可以减少空间上的浪费，并可以进行空间的动态扩展。例如：

就是说数组的大小是：1,2,4,8,16,32,64,128,256....

也就是扩容的时候，每次都把空间翻一倍，和C++里面的vector是一样的。

如果我在a[0]位置插入一个值，那么数组的大小就是1，数组的利用率就是100%。

接着在a[100]位置插入一个值，那么数组的大小就要扩大到128，那就有126个位置是浪费的。

那如果在a[10000]位置插入一个值，那么10000多个位置是浪费的，空间浪费极大。

所以用哈希表做了优化：

在往数组中插入一个key时，我们可以计算出两个新的数值：

1. 新数组的大小

2. 插入这个key之后，新数组中元素的个数

如果 新数组中元素的个数 > 新数组的大小/2，那就插入数组。否则就把key放到哈希表中去。

也就是数组的利用率要超过50%。

a［0］ = 1, a［1］ = 1, a［5］= 1

结果分析：数组大小4, hash大小1，a［5］本来是在8这个区间里的，但是有用个数3 < 8 / 2，所以a［5］放在了hash表里。

a［0］ = 1, a［1］ = 1, a［5］ = 1, a［6］ = 1,

结果分析：数组大小4，hash大小2,有用个数4 < 8 / 2，所以a［5］,a［6］放在hash表里。

a［0］ = 1, a［1］ = 1, a［5］ = 1, a［6］ = 1, a［7］ = 1

结果分析：数组大小8，hash大小0, 有用个数5 > 8 / 2。

<a href="http://blog.csdn.net/zr339361504/article/details/52432163" target="_blank">http://blog.csdn.net/zr339361504/article/details/52432163</a>
