---
layout: post
title: "[Art][动态规划] 股票买卖系列"
date: 2019-03-09
author: mdgsf
comments: true
categories: Art
tags: [Art,Algorithm,动态规划]
description:
published: true #default true
---

## 题目链接

[121 买卖股票的最佳时机](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock/)

[122 买卖股票的最佳时机 II](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-ii/)

[123 买卖股票的最佳时机 III](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-iii/)

[188 买卖股票的最佳时机 IV](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-iv/)

[309 最佳买卖股票时机含冷冻期](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-with-cooldown/)

[714 买卖股票的最佳时机含手续费](https://leetcode-cn.com/problems/best-time-to-buy-and-sell-stock-with-transaction-fee/)

## 121 买卖股票的最佳时机

只能够买一卖一次。

### 方法一

不需要用动态规划，遍历一遍数组即可。

DP[i] = prices[i] - min(prices[0], prices[1], ..., prices[i])

Result = max(DP[0], DP[1], ..., DP[i])

DP[i] 就是从 0 到 i 之间任意一个点买入，在 i 这个点卖出，能够获得的最大收益。

```golang
func maxProfit(prices []int) int {
    if len(prices) == 0 {
        return 0
    }
    
    min := prices[0]
    res := 0
    for i := 1; i < len(prices); i++ {
        if prices[i] < min {
            min = prices[i]
        }
        curResult := prices[i] - min
        if curResult > res {
            res = curResult
        }
    }
    return res
}
```

## 122 买卖股票的最佳时机 II

买卖可以有无数次，但是不能重叠。

这个题目就更简单了，只要第 i 天的大于第 i-1 天的，就买入卖出。

### 代码

```golang
func maxProfit(prices []int) int {
    res := 0
    for i := 1; i < len(prices); i++ {
        if prices[i] > prices[i-1] {
            res += (prices[i] - prices[i-1])
        }
    }
    return res
}
```

## 123 买卖股票的最佳时机 III

最多可以完成两笔交易。

### 解析

我们尝试定义状态：

```
DP[i] 为到第 i 天的最大利润
DP[i-1] 为到第 i-1 天的最大利润
```

尝试定义状态转移方程：

```
DP[i] = DP[i-1] + (-a[i]) //买入股票
      = DP[i-1] + (a[i])  //卖出股票
```

那么，这个时候你会发现我们无法判断到底是要买入股票，还是卖出股票。所以这个状态还不够，我们尝试再增加一个维度。

再次尝试定义状态：

```
DP[i][j] 
i 表示数组的下标，表示第 i 天
j 可以为 0 或者 1。
j = 0 表示手上没有股票。
j = 1 表示手上有股票。
DP[i][0] 就是到第 i 天的最大利润，且这时手上没有股票。
DP[i][1] 就是到第 i 天的最大利润，且这时手上有股票。
```

状态转移方程如下：

```
DP[i][0] = Max(
    DP[i-1][0]        //不动，就是在第 i-1 天的时候，手上没有股票。
    DP[i-1][1] + a[i] //卖出股票，就是在第 i-1 天的时候，手上有股票。
)
DP[i][1] = Max(
    DP[i-1][1]        //不动，就是在第 i-1 天的时候，手上有股票。
    DP[i-1][0] - a[i] //买入股票，就是在第 i-1 天的时候，手上没有股票。
)
```

这个时候，这个状态转移方程已经能够解决 `121` 那个只买卖一次的题目了，DP[n-1][0] 就是买卖一次的最大利润。

但是这个方程中还没有完成 K 笔交易的信息，`123` 这个题目是完成两笔交易，所以还可能是 4 笔、５ 笔、...。

所以我们再次尝试添加一个新的维度。

再次尝试定义状态：

```
DP[i][k][j]
i 取值为 0 到 n-1，表示数组的下标，表示第 i 天。（这里 n 就是数组的长度）
j 取值为 0 或者 1。
j = 0 表示手上没有股票。
j = 1 表示手上有股票。
DP[i][0] 就是到第 i 天的最大利润，且这时手上没有股票。
DP[i][1] 就是到第 i 天的最大利润，且这时手上有股票。
k 取值为 0 到 K，表示之前已经交易了多少次。
```

状态转移方程：

```
for i := 0; i < n-1; i++ {
    for k := 0; k < K; k++ {
        DP[i][k][0] = Max (
            DP[i-1][k][0]          //不动
            DP[i-1][k-1][1] + a[i] //卖出一股股票
        )
        DP[i][k][1] = Max (
            DP[i-1][k][1]          //不动
            DP[i-1][k][0] - a[i] //买入一股股票
        )
    }
}
```

最后结果就是

```
DP[n-1, {0~K}, 0] = Max(
    DP[n-1][0][0] //在第 n-1 天，手上没有股票，交易了 0 次。
    DP[n-1][1][0] //在第 n-1 天，手上没有股票，交易了 1 次。
    ...
    DP[n-1][K][0] //在第 n-1 天，手上没有股票，交易了 K 次。
)
```

### 代码

```golang
func maxProfit(prices []int) int {
	n := len(prices)
	const K int = 2

	if n == 0 {
		return 0
	}

	DP := make([][K + 1][2]int, n)

	maxint := int(^uint32(0) >> 1)
	minint := ^maxint

	DP[0][0][0] = 0          //第 0 天，什么也没做
	DP[0][0][1] = -prices[0] //第 0 天，买入股票
	DP[0][1][0] = minint     //no
	DP[0][1][1] = minint     //no
	DP[0][2][0] = minint     //no
	DP[0][2][1] = minint     //no

	for i := 1; i < n; i++ {
		for k := 0; k <= K; k++ {
			if k == 0 {
				DP[i][0][0] = DP[i-1][0][0]
			} else {
				DP[i][k][0] = max(DP[i-1][k][0], DP[i-1][k-1][1]+prices[i])
			}

			DP[i][k][1] = max(DP[i-1][k][1], DP[i-1][k][0]-prices[i])
		}
	}

	m := DP[n-1][0][0]
	for k := 1; k <= K; k++ {
		if DP[n-1][k][0] > m {
			m = DP[n-1][k][0]
		}
	}
	return m
}

func max(a, b int) int {
	if a > b {
		return a
	}
	return b
}
```

## 188 买卖股票的最佳时机 IV

思路见上面一题 123。

```golang
func maxProfit(k int, prices []int) int {
    n := len(prices)
	if n == 0 {
		return 0
	}
    
    if k > len(prices)/2 { //当 k 特别大的时候，做特殊处理
        max := 0
        for i := 1; i < len(prices); i++ {
            if prices[i] > prices[i-1] {
                max += (prices[i] - prices[i-1])
            }
        }
        return max
    }

	DP := make([][][2]int, n)
    for i := range DP {
        DP[i] = make([][2]int, k+1)
    }

	maxint := int(^uint32(0) >> 1)
	minint := ^maxint

	DP[0][0][0] = 0          //第 0 天，什么也没做
	DP[0][0][1] = -prices[0] //第 0 天，买入股票
    for kk := 1; kk < k; kk++ {
        DP[0][kk][0] = minint
        DP[0][kk][1] = minint
    }

	for i := 1; i < n; i++ {
		for kk := 0; kk <= k; kk++ {
			if kk == 0 {
				DP[i][0][0] = DP[i-1][0][0]
			} else {
				DP[i][kk][0] = max(DP[i-1][kk][0], DP[i-1][kk-1][1]+prices[i])
			}

			DP[i][kk][1] = max(DP[i-1][kk][1], DP[i-1][kk][0]-prices[i])
		}
	}

	m := DP[n-1][0][0]
	for kk := 1; kk <= k; kk++ {
		if DP[n-1][kk][0] > m {
			m = DP[n-1][kk][0]
		}
	}
	return m
}

func max(a, b int) int {
	if a > b {
		return a
	}
	return b
}
```

## 309 最佳买卖股票时机含冷冻期

## 714 买卖股票的最佳时机含手续费

