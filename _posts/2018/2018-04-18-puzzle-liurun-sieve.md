---
layout: post
title:  "[算法学习][智力题] 骰子日历"
date:   2018-04-18
comments: true
categories: Art
tags: []
description: ""
published: true
---

**问题：**

有2个骰子,每一个骰子都是6面的正方体,每一面上只能放0到9的数字一个,问这2个骰子如何组合，可以达到显示日历的效果（从01-31）？

答案见底部 <br />
| <br />
| <br />
| <br />
| <br />
| <br />
| <br />
| <br />
| <br />
| <br />
| <br />
| <br />
| <br />
| <br />
| <br />
| <br />
| <br />
| <br />
| <br />
| <br />
| <br />
| <br />
| <br />
| <br />
| <br />
| <br />
| <br />
| <br />
| <br />
| <br />
| <br />
| <br />
| <br />
|/ <br />

**分析：**

这道题的逻辑是这样的。首先，大多数人都会想到，我们有两个立方体，那就一共12个面。现在有0-9，一共10个数，放到这12个面上，所以，一定有数字是重复出现在两个立方体上的。

那么，哪些数是会重复出现的呢？

想到我们的目的是用这两个立方体表示日历，也就是01-31这一串数字。那么，有哪些数字是在个位和十位上都必须有的呢？

我们有11号和22号，所以1和2这两个数字在两个立方体上必须都出现，那这样一算，正好就是12个数字和12个面，可以一一对应了。

但是如果你细想想，就会发现不对啊，当日期是一位数的时候，0还需要在十位的位置上补位呢，所以0也必须同时出现在两个立方体上。

如果0也必须出现2次，那就有13个数字，要出现在12个面上了。这样就少了一个面。怎么办？

其实如果你能想到这里，就已经能拿到一半的分了。

那少的这一面该怎么办？怎么在12个面上放13个数字？有没有数字能重复用？

有，那就是6和9。到这为止，这个问题就解决了。

那这个问题考核的是什么呢？

这里的考点叫"跨越思维"，也就是跳出固定框架去思考的能力。如果你觉得6就是6，9就是9，那就是没有跳出固定的思维框架。

这种跨越思维的能力，在现实生活中，极其重要。

比如，谁说冰箱的冰格，一定要在冰箱里面呢？如果把冰格放满厨房呢？这就是"分布式冰箱"。跨越思维，是创新的源泉。我们对创新能力要求高的人，非常重视对这种能力的考核。

同样，如果我感觉到你对这道题很熟悉，后面还有几十道其他类似的题等着。再次记住，思维方式，比答案重要。

**来源：** 刘润老师的微信公众号
