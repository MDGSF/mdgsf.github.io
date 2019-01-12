---
layout: post
title:  "[字符串] KMP"
date:   2016-04-14
comments: true
categories: Art
tags: c,kmp
description:
published: true
---

### 问题

我们首先要明确，我们要做的事情是什么：**给定字符串M和N（M.length >= N.length），请找出N在M中出现的匹配位置**。
说白了，就是一个简单的字符串匹配。或许你会说这项工作没什么难度啊，其实只要从头开始比较两个字符串对应字符相等与否，不相等就再从M的下一位开始比较就好了么。是的，这就是一个传统的思路，总结起来其思想如下：

1. 当 m[j] == n[i] 时，i与j同时+1；
2. 当 m[j] != n[i] 时，j回溯到j-i+1，i回溯到0，然后回到第一步；
3. 当 i == len(n) 时，说明匹配完成，输出一个匹配位置，之后回到第二步，查找下一个匹配点。

我们举个例子来演示一下这个比较的方法，给定字串M - abcdabcdabcde，找出N - abcde这个字符串。传统思路解法如下：

```
i: 0 1 2 3 4 5 6 7 8 9 0 1 2
M: a b c d a b c d a b c d e
N: a b c d e                 // 匹配四位成功后发现a、e不匹配

i: 0 1 2 3 4 5 6 7 8 9 0 1 2
M: a b c d a b c d a b c d e
N:   a b c d e               // 发现 a、b不匹配

i: 0 1 2 3 4 5 6 7 8 9 0 1 2
M: a b c d a b c d a b c d e
N:     a b c d e             // 发现 a、c不匹配

i: 0 1 2 3 4 5 6 7 8 9 0 1 2
M: a b c d a b c d a b c d e
N:       a b c d e           // 发现 a、d不匹配

i: 0 1 2 3 4 5 6 7 8 9 0 1 2
M: a b c d a b c d a b c d e
N:         a b c d e         // 匹配四位成功后发现a、e不匹配

i: 0 1 2 3 4 5 6 7 8 9 0 1 2
M: a b c d a b c d a b c d e
N:           a b c d e       // 发现 a、b不匹配

i: 0 1 2 3 4 5 6 7 8 9 0 1 2
M: a b c d a b c d a b c d e
N:             a b c d e     // 发现 a、c不匹配

i: 0 1 2 3 4 5 6 7 8 9 0 1 2
M: a b c d a b c d a b c d e
N:               a b c d e   // 发现 a、d不匹配

i: 0 1 2 3 4 5 6 7 8 9 0 1 2
M: a b c d a b c d a b c d e
N:                 a b c d e // 匹配成功
```



## KMP

目标字符串记为：Target, 长度记为：n。

搜索字符串记为：Pattern, 长度记为：m。


KMP字符串模式匹配通俗点说就是一种在一个字符串中定位另一个串的高效算法。简单匹配算法的时间复杂度为O((n-m+1)m);KMP匹配算法。可以证明它的时间复杂度为O(n+m)。


### The Partial Match Table

The key to KMP, of course, is the partial match table. The main obstacle between me and understanding KMP was the fact that I didn’t quite fully grasp what the values in the partial match table really meant. I will now try to explain them in the simplest words possible.

Here’s the partial match table for the pattern “abababca”:

```
char:  | a | b | a | b | a | b | c | a |
index: | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 |
value: | 0 | 0 | 1 | 2 | 3 | 4 | 0 | 1 |
```

If I have an eight-character pattern (let’s say “abababca” for the duration of this example), my partial match table will have eight cells. If I’m looking at the eighth and last cell in the table, I’m interested in the entire pattern (“abababca”). If I’m looking at the seventh cell in the table, I’m only interested in the first seven characters in the pattern (“abababc”); the eighth one (“a”) is irrelevant, and can go fall off a building or something. If I’m looking at the sixth cell of the in the table… you get the idea. Notice that I haven’t talked about what each cell means yet, but just what it’s referring to.

Now, in order to talk about the meaning, we need to know about **proper prefixes** and **proper suffixes**.

**Proper prefix**: All the characters in a string, with one or more cut off the end. “S”, “Sn”, “Sna”, and “Snap” are all the proper prefixes of “Snape”.

**Proper suffix**: All the characters in a string, with one or more cut off the beginning. “agrid”, “grid”, “rid”, “id”, and “d” are all proper suffixes of “Hagrid”.

With this in mind, I can now give the one-sentence meaning of the values in the partial match table:

**The length of the longest proper prefix in the (sub)pattern that matches a proper suffix in the same (sub)pattern.**

Let’s examine what I mean by that. Say we’re looking in the third cell. As you’ll remember from above, this means we’re only interested in the first three characters (“aba”). In “aba”, there are two proper prefixes (“a” and “ab”) and two proper suffixes (“a” and “ba”). The proper prefix “ab” does not match either of the two proper suffixes. However, the proper prefix “a” matches the proper suffix “a”. Thus, **the length of the longest proper prefix that matches a proper suffix**, in this case, is 1.

Let’s try it for cell four. Here, we’re interested in the first four characters (“abab”). We have three proper prefixes (“a”, “ab”, and “aba”) and three proper suffixes (“b”, “ab”, and “bab”). This time, “ab” is in both, and is two characters long, so cell four gets value 2.

Just because it’s an interesting example, let’s also try it for cell five, which concerns “ababa”. We have four proper prefixes (“a”, “ab”, “aba”, and “abab”) and four proper suffixes (“a”, “ba”, “aba”, and “baba”). Now, we have two matches: “a” and “aba” are both proper prefixes and proper suffixes. Since “aba” is longer than “a”, it wins, and cell five gets value 3.

Let’s skip ahead to cell seven (the second-to-last cell), which is concerned with the pattern “abababc”. Even without enumerating all the proper prefixes and suffixes, it should be obvious that there aren’t going to be any matches; all the suffixes will end with the letter “c”, and none of the prefixes will. Since there are no matches, cell seven gets 0.

Finally, let’s look at cell eight, which is concerned with the entire pattern (“abababca”). Since they both start and end with “a”, we know the value will be at least 1. However, that’s where it ends; at lengths two and up, all the suffixes contain a c, while only the last prefix (“abababc”) does. This seven-character prefix does not match the seven-character suffix (“bababca”), so cell eight gets 1.


### How to use the Partial Match Table

We can use the values in the partial match table to skip ahead (rather than redoing unnecessary old comparisons) when we find partial matches. The formula works like this:

If a partial match of length **partial_match_length** is found and `table[partial_match_length] > 1`, we may skip ahead `partial_match_length - table[partial_match_length - 1]` characters.

Let’s say we’re matching the pattern “abababca” against the text “bacbababaabcbab”. Here’s our partial match table again for easy reference:

```
char:  | a | b | a | b | a | b | c | a |
index: | 0 | 1 | 2 | 3 | 4 | 5 | 6 | 7 |
value: | 0 | 0 | 1 | 2 | 3 | 4 | 0 | 1 |
```

The first time we get a partial match is here:

```
bacbababaabcbab
 |
 abababca
```

This is a partial_match_length of 1. The value at `table[partial_match_length - 1]` (or `table[0]`) is 0, so we don’t get to skip ahead any. The next partial match we get is here:

```
bacbababaabcbab
    |||||
    abababca
```

This is a partial_match_length of 5. The value at `table[partial_match_length - 1]` (or `table[4]`) is 3. That means we get to skip ahead `partial_match_length - table[partial_match_length - 1]` (or `5 - table[4]` or `5 - 3` or `2`) characters:


```
// x denotes a skip

bacbababaabcbab
    xx|||
      abababca
```

This is a partial_match_length of 3. The value at `table[partial_match_length - 1]` (or `table[2]`) is 1. That means we get to skip ahead `partial_match_length - table[partial_match_length - 1]` (or `3 - table[2]` or `3 - 1` or `2`) characters:

```
// x denotes a skip

bacbababaabcbab
      xx|
        abababca
```

At this point, our pattern is longer than the remaining characters in the text, so we know there’s no match.


### 如何快速预处理Pattern字符串，得到table数组。

这里，我们将 Pattern字符串记为B[]， table 数组记为P[]。

对于字符串 "ababacb"。我们可以通过P[0],P[1],…,P[j-1]的值来获得P[j]的值。

假如我们已经求出了P[0],P[1],P[2]和P[3]，看看我们应该怎么求出P[4]和P[5]。

**求P[4]**。P[3]=2，那么P[4]显然等于P[3]+1。

```
因为由P[3]=2 ==> 所以B[0,1]和B[2,3]相等，
  即B[0]=B[2]='a'
    B[1]=B[3]='b'

现在又有 B[ P[3] ]==B[4]   
==> 即  B[2]==B[4]
==> 所以P[4]=P[3]+1
```

```
char(B):  | a | b | a | b | a | c | b |
index:    | 0 | 1 | 2 | 3 | 4 | 5 | 6 |
value(P): | 0 | 0 | 1 | 2 | 3 | ? |   |
```

**求P[5]**。P[5]也等于P[4]+1吗？显然不是，因为B[ P[4] ]<>B[5]。
那么，我们要考虑“退一步”了。我们考虑P[5]是否有可能由P[4]的情况所包含的子串得到，即是否P[5]=P[ P[4]-1 ]+1。
这里想不通的话可以仔细看一下：

P[4]=3是因为B[0..2]和B[2..4]都是"aba"；而P[2]=1则告诉我们，B[0]、B[2]是"a"。

==>B[0]、B[2]和B[4]都是"a"。

==>即**B[0]和B[4]相等**。

既然P[5]不能由P[4]得到，或许可以由P[2]得到（如果B[1]恰好和B[5]相等的话，P[5]就等于P[2]+1了）。显然，P[5]也不能通过P[2]得到，因为B[1]<>B[5]。事实上，这样一直推到P[0]也不行，最后，我们得到，P[5]=0。


### Code

```cpp
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

/*
 * @param pcTarget: the target string.
 * @param pcPattern:
 * @return : the index pcPattern founded in pcTarget if success,
 *           -1 if failed.
 */
int iKMP(const char * pcTarget, const char * pcPattern)
{
    int i = 0;
    int iTargetLen = strlen(pcTarget);
    int iPatternLen = strlen(pcPattern);

    /* build piTable */
    int * piTable = (int*)malloc(sizeof(int)*iPatternLen);
    int iMaxLen = 0;
    piTable[0] = 0;
    for (i = 1; i < iPatternLen; i++)
    {
        while (iMaxLen>0 && pcPattern[i] != pcPattern[iMaxLen])
            iMaxLen = piTable[iMaxLen-1];
        if(pcPattern[i] == pcPattern[iMaxLen])
            iMaxLen++;
        piTable[i] = iMaxLen;
    }
    /* build piTable end */

    int iEndIndex = iTargetLen - iPatternLen;
    for (i = 0; i <= iEndIndex;)
    {
        int j = 0;
        while (j < iPatternLen && pcTarget[i] == pcPattern[j]) {
            i++;
            j++;
        }

        if(j == iPatternLen) {
            return i - iPatternLen;
        }

        if(j > 1) {
            int iSkip = j - piTable[j-1];
            i += iSkip;
        } else {
            i++;
        }
    }

    return -1;
}

int main()
{
    const char * pcTarget = "sdfjskafjsfks";
    const char * pcPattern = "ska";
    printf("%s\n%s\n", pcTarget, pcPattern);
    printf("%d\n", iKMP(pcTarget, pcPattern) );
    return 0;
}
```

### 链接

[The Knuth-Morris-Pratt Algorithm](http://jakeboxer.com/blog/2009/12/13/the-knuth-morris-pratt-algorithm-in-my-own-words/)

[KMP字符串模式匹配详解](http://blog.chinaunix.net/uid-27164517-id-3280128.html)

[matrix67 KMP算法详解](http://www.matrix67.com/blog/archives/115)

[ituring KMP算法解析](http://www.ituring.com.cn/article/59881)
