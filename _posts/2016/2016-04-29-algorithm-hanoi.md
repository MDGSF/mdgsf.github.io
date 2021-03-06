---
layout: post
title:  "[递归] 汉诺塔"
date:   2016-04-29
comments: true
categories: Art
tags: [Art,Algorithm,C,Tower of hanoi]
description:
published: true
---

### 问题

汉诺塔：汉诺塔（又称河内塔）问题是源于印度一个古老传说的益智玩具。大梵天创造世界的时候做了三根金刚石柱子，在一根柱子上从下往上按照大小顺序摞着64片黄金圆盘。大梵天命令婆罗门把圆盘从下面开始按大小顺序重新摆放在另一根柱子上。并且规定，在小圆盘上不能放大圆盘，在三根柱子之间一次只能移动一个圆盘。

### code

```c++
#include <stdio.h>

void hanoi(int n, char a, char b, char c)
{
    if(n == 1)
    {
        printf("%c --> %c\n", a, c);
        return ;
    }
    hanoi(n-1, a, c, b);
    hanoi(1, a, b, c);
    hanoi(n-1, b, a, c);
}

int main()
{
    hanoi(3, 'a', 'b', 'c');
    getchar();
    return 0;
}
```

解释下上面的这段代码：

现在有3根柱子，这里我们叫做A柱子，B柱子，C柱子。有n个圆盘在A柱子上，我们要将这n个圆盘移动到C柱子上。

**如果n=1**，也就是只有一个圆盘，那我们直接将这个圆盘移动到C柱子上就可以了。

**如果n=2**，那只要先将上面的那个小圆盘移动到B柱子上，然后再将下面的大圆盘移动到C柱子，再将小圆盘从B柱子移动到C柱子。

**如果n=3**，圆盘有点多了，我们这里给圆盘排个顺序，从上往下叫做`圆盘1`，`圆盘2`，`圆盘3`。

在这里我们将 圆盘1 和 圆盘2 看做一个整体，叫做`圆盘[1 - 2]`，这个名字是我随便取的。那我们要做的就是先将`圆盘[1 - 2]` 移动到B柱子，具体怎么移动我们先不管。

假设说我们已经成功的将 `圆盘[1 - 2]` 移动到了B柱子上，那这个时候A柱子上就只有一个圆盘C了，那我们直接将圆盘3从A柱子上移动到C柱子上就可以了。

最后再将 `圆盘[1 - 2]` 这个整体从B柱子移动到C柱子上就完成了。

那 `圆盘[1 - 2]` 这个整体是怎么从A柱子上移动到B柱子上的呢？又是怎么从B柱子上移动到C柱子上的呢？请再回头看看当`n=2`的时候的情况。

<hr />

**如果就是有n个圆盘呢？**

我们这里给圆盘排个顺序，从上往下叫做`圆盘1`，`圆盘2`，`圆盘3` .... ，`圆盘n-1`，`圆盘n`。

因为小个的盘子只能在大盘子的上面，那`圆盘1`就是最小的盘子，`圆盘n`就是最大的盘子，移动到C柱子的时候，`圆盘n`一定是在最下面的。

根据上面的分析，我们只需要：

1. 先将 `圆盘1` 到 `圆盘n-1` 的一共n-1个圆盘看做一个整体，这里叫做 `圆盘[1 - (n-1)]`。
2. 将 `圆盘[1 - (n-1)]` 这个整体从A柱子移动到B柱子。
3. 将 `圆盘n` 从A柱子移动到C柱子。
4. 将 `圆盘[1 - (n-1)]` 这个整体从B柱子移动到C柱子。

这里我们写一个函数来实现这个功能

```c++
void hanoi(int n, char a, char b, char c)
{
    ...
}
```

假设这个函数的功能就是将n个盘子从A柱子移动到C柱子，中间借用了B柱子。先不要管这个函数是怎么实现的。

看下面的调用代码：

```c++
//将n个盘子从A柱子移动到C柱子，中间借用了B柱子
hanoi(n, 'a', 'b', 'c')

//步骤二：将n-1个盘子从A柱子移动到B柱子，中间借用了C柱子
hanoi(n-1, 'a', 'c', 'b')

//步骤三：将1个盘子从A柱子移动到C柱子，中间借用了B柱子
hanoi(1, 'a', 'b', 'c')

//步骤三：将n-1个盘子从B柱子移动到C柱子，中间借用了A柱子
hanoi(n-1, 'b', 'a', 'c')
```

再回头看看上面给出的代码，修改代码中n的数量，输出看看结果，多运行几次就好了。

### 补充

`n` 代表有n个圆盘

`f(n)` 代表有n个圆盘的时候移动了 f(n) 次 才把n个圆盘从A柱子移动到C柱子。

先给出两个结论：

1. f(n) = 2 * f(n-1) + 1
2. f(n) = 2^n - 1

**证明结论一：**

```
1. 有n-1个圆盘，从A柱子移动到C柱子一共需要 f(n-1) 次。
2. 有n个圆盘时，需要先将n-1个圆盘从A柱子移动到B柱子，需要 f(n-1) 次。
2. 再将第n个圆盘从A柱子移动到C柱子，需要 1 次。
3. 最后再把 n-1 个圆盘从B柱子移动到C柱子，需要 f(n-1) 次。

所以一共移动了 f(n) = f(n-1) + 1 + f(n-1) = 2 * f(n-1) + 1
```

**证明结论二：**

```
f(1) = 1
f(2) = 2*1 + 1 = 3    = 2^2 - 1
f(3) = 2*3 + 1 = 7    = 2^3 - 1
f(4) = 2*7 + 1 = 15   = 2^4 - 1
...

所以我们猜测 f(n) = 2^n - 1

用数学归纳法：

(1)当n=1时
f(n) = 2^1 - 1 = 1
成立
(2)假设当n=m时，公式成立，即
f(m) = 2^m - 1

那么：
f(m+1) = 2*f(m) + 1
       = 2*(2^m - 1) + 1
       = 2*2^m - 2 + 1
       = 2^(m+1) - 1
所以公式成立。


```
