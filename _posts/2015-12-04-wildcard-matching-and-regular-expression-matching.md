---
layout: post
title: "Wildcard Matching and Regular Expression Matching"
description: ""
category: tech
tags: [Python, Algorithm]
---
{% include JB/setup %}

## About ##
这两道题非常像，但是又有区别，DP经典又常考，edge case也很多，所以还是觉得把这两道题的思路写下来，下次碰到就不会两眼发黑直接当机了。。。

## Wildcard Matching##
先从简单的 wildcard matching 开始，具体题目见 [leetcode wildcard matching](https://leetcode.com/problems/wildcard-matching/)。

DP初始化，row 是 `len(s)+1`，col 是 `len(p)+1`，`res[0][0] = True`，剩下的是 `False`。那么 `res[i][j]` 就是代表 `s[:i]` 到 `p[:j]` 是不是满足 matching。

第一个要考虑的 edge case 是 `len(s) = 0`，也就是 matrix 的第一行。对于题目要求
> '?' Matches any single character.

> '*' Matches any sequence of characters (including the empty sequence).

这种情况下，只要我们一直碰到 `p[i] == '*'`，就没有问题，但是当碰到第一个 `p[i] != '*'`，之后就全是 `False` 了。也就是说 `res[0][i] = res[0][i-1] and p[i-1] == '*'`。

接下来就是遍历矩阵，处理一下三种情况就好了：

#### `p[i]` 是普通字符 ####
这个最简单，没啥好说的，`res[i][j] = res[i-1][j-1] and (s[i-1] == p[j-1])`。

#### `p[i] == '?'` ####
跟前者不会有太大区别，因为 `?` 可以代替任意单个字符，所以不用检查当前字符的匹配，直接搬用上次的结果，`res[i][j] = res[i-1][j-1]`。

#### `p[i] == '*'` ####
因为 `*` 可以代替任意字符串甚至空串，那么我们就分几种情况讨论：

##### `p[i] = '*'` 代替空字符串 #####
既然是空串，那么 `p[:i]` 跟 `p[:i-1]` 就是一样的，那么就很自然的得出：`res[i][j] = res[i][j-1]`

##### `p[i] = '*'` 代替任意单个字符 #####
这个情况跟 `p[i] = '?'` 就是一样情形，所以 `res[i][j] = res[i-1][j-1]`

##### `p[i] = '*'` 代替两个或以上长度字符串 #####
这个情况就是如果 `res[i-1][j]` 匹配，那么在 `j` 不动的情况下，`res[i][j]` 到 `res[MAX_ROW][j]` 都是 `True`。不过映射到矩阵上我们只要声明 `res[i][j] = res[i-1][j]` 即可。

到此为止 DP 的状态转移分析结束，那么也就很好写了，可以在开始加一点判断 edge case，比如输入字符串非常非常大的时候，先剔除 p 中的 `*`，判断一下长度之类的，防止 TLE，也应该事先问一下面试官对于空字符串的输入，又没有什么特殊的返回值要求，代码如下所示：

    class Solution(object):
        def isMatch(self, s, p):
            """
            :type s: str
            :type p: str
            :rtype: bool
            """
            if len(p) - p.count('*') > len(s):
                return False
            row, col = len(s), len(p)
            res = [[False for _ in range(col+1)] for _ in range(row+1)]
            res[0][0] = True
            for i in range(1, col+1):
                res[0][i] = res[0][i-1] and (p[i-1] == '*')
            for i in range(1, row+1):
                for j in range(1, col+1):
                    if p[j-1] == '*':
                        res[i][j] = res[i-1][j] or res[i][j-1] or res[i-1][j-1]
                    elif p[j-1] == '?':
                        res[i][j] = res[i-1][j-1]
                    else:
                        res[i][j] = res[i-1][j-1] and (s[i-1] == p[j-1])
            return res[-1][-1]

## Regular Expression Matching ##
跟前者非常像，原题见 [leetcode regular expression matching](https://leetcode.com/problems/regular-expression-matching/)。

同样的 DP 的初始化，就不说了。但是要注意题设的一个区别：
> '*' Matches zero or more of the preceding element.

对于 wildcard matching 是任意长度包括 0 的字符串，这里是任意长度包括0的**之前那个字符**。那么类似的，要处理的第一个 edge case 就是 `len(s) = 0` 的情况。首先排除 p 是非法输入的情况，既第一个 char 就是 `*`。然后对于 p 来说，所有类似 `a*`，`a**` 的都是可以 match 空字符串，但是类似 `aa*` 就不行了。所以第一行的初始化是这样的：

* 对于 `p[i] != '*'`，如果 `p[i-1] != '*'`，那么从 `res[i]` 开始都是 False。
* 对于 `p[i] ＝= '*'`，如果 `p[i-1] != '*'` 或 `res[0][i] = True`，那么 `res[i+1] = True`。

然后就是跟一样的遍历矩阵，处理 `p[i]` 的情况就好了，然后前两种情况是一样的，就不多说了，重点看下 `p[i] = '*'` 的处理方法。在这个时候 `p[i]` 可以匹配 0 个，1 个，甚至多个 `p[i-1]`。

#### 匹配 0 个 `p[i-1]` ####
这个时候可以看到 `p[:i] = p[:i-2]`，因为我们一下子消掉了两个字符，所以 `res[i][j] = res[i][j-2]`。

#### 匹配 1 个 `p[i-1]` ####
跟上面的情况几乎一样，只是消掉了一个字符，也就是那个 `*`，`res[i][j] = res[i][j-1]`。

#### 匹配 2 到多个 `p[i-1]` ####
在这个情况下 `p[:i] = p[:i-1] + p[i-1] + p[i-1] + ...`，这个时候匹配的前提跟第一题略相似，不过有一个隐含条件是 `p[i-1]` 必须匹配 `s[i]`，所以可以得出 `res[i][j] = res[i][j-1] or res[i][j-2] or (res[i-1][j] and (p[j-2] == s[i-1] or p[j-2] == '.'))`。

至此，代码如下：

    class Solution(object):
        def isMatch(self, s, p):
            """
            :type s: str
            :type p: str
            :rtype: bool
            """
            row, col = len(s), len(p)
            res = [[False for _ in range(col+1)] for _ in range(row+1)]
            res[0][0] = True
            i = 2
            while i <= col:
                if p[i-1] != '*' and p[i-2] != '*':
                    break
                if p[i-1] == '*' and (p[i-2] != '*' or res[0][i-1]):
                    res[0][i] = True
                i += 1
            for i in range(1, row+1):
                for j in range(1, col+1):
                    if p[j-1] != '.' and p[j-1] != '*':
                        res[i][j] = res[i-1][j-1] and (s[i-1] == p[j-1])
                    elif p[j-1] == '.':
                        res[i][j] = res[i-1][j-1]
                    elif p[j-1] == '*':
                        res[i][j] = res[i][j-1] or res[i][j-2] or (res[i-1][j] and (p[j-2] == s[i-1] or p[j-2] == '.'))
            return res[-1][-1]
