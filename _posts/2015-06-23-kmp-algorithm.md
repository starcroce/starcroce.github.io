---
layout: post
title: "KMP Algorithm"
description: ""
category: tech 
tags: [Python, Algorithm]
---
{% include JB/setup %}

## 杂 ##

我一直觉得，在一般的面试题中，KMP 都是一个屠龙之技然并卵的存在，毕竟如果真的问到 strstr 的题目，即使不考虑非法输入的情况下，能够白板一遍 bugfree 写出暴力解已经差不多了，不过考虑到离我跳槽还有至少半年的时间，那么就当复习下众多经典算法也好。


## 什么是 KMP ##

我是必然没有兴趣拿 wiki 砸过来的，所以就举个栗子吧：
    
	s1 = abababcab
	s2 = ababc

一开始从 `i = j = 0` 开始比较，可见当 `i = j = 4` 的时候 `s1[i] != s2[j]`，所以在暴力解的情况下我们要做的是将 `i` reset 到 1，同时将 `j` reset到 0，并重新开始比较。这个时候就发现了 `i` 其实是有回溯的，这也就造成了暴力解的复杂度会变成 O(n^2)。

但是我们总归是可以做出一点优化的。当 `i = j = 4` 的时候，我们知道了其实之前都是完全匹配的，并且 `s2` 的前四位是 `abab`。在这种情况下其实完全没有必要继续比较 `s1[1]` 和 `s2[0]`，因为这个肯定不相等。肉眼观察出的可以实现的优化是比较 `s1[2]` 和 `s1[0]`。也就是如下的比较方式:

	s1: abababcab
	s2:   ababc

这个的优化操作就是，固定住 `i = 4`，并将其与 `s2[2]` 进行比较。原因就是在 `s1[i]` 与 `s2[4]` 匹配失败的时候，我们确定的是 `s1[i-4:i] = abab`，这样我们要做的就是让 `s2` 对齐 `s1` 的第二个 ab，这样的话我们可以跳过比较 `s1[i-2:i]` 与 `s2[:2]`，因为他们都是 ab。我们可以直接开始比较 `s1[i]` 和 `s2[2]`，如果成功，就可以继续比较下一个，如果不成功，则继续用上述的优化手段。

于是我们就可以引入一个跳转表 (jump table) 的概念。根据上面的例子， 在 `s1[i]` 与 `s2[4]` 匹配失败的时候，我们跳转到 `s2[2]`，开始比较 `s1[i]` 与 `s2[2]`。所以在跳转表上，4 对应的跳转值是 2。如果我们有了这么一个关于 `s2` 所有 index 的跳转表的话，整个的匹配过程就会非常简单，大体上如下所示:

	while i < len(s1) and j < len(s2):
		if s1[i] == s2[j]:
			i += 1
			j += 1
		else:
			j = jump_table[j]
			# i += 1 if we need to compare from the s2 start

注意我上面只是**伪代码**。


## 跳转表 ##

现在问题变成了如何生成跳转表。假设跳转表是 `next[]`，我们开始慢慢分情况讨论：

### 如果 `s1[i]` 和 `s2[0]` 匹配失败：###

那就没啥好说的，直接开始比较 `s1[i+1]` 和 `s2[0]`，没啥优化可言。这个情况下我们用 `next[0] = -1` 这么一个特殊值说明需要移动 `s1` 的 index。

### 如果 `s1[i]` 和 `s2[1]` 匹配失败：###

也没啥好说的，开始比较 `s1[i]` 和 `s2[0]`，于是我们知道 `next[1] = 0`。

### 如果 `s1[i]` 和 `s2[2]` 匹配失败：###

这个时候就有可以优化的地方了，具体要取决与 `s2[1]` 的值。举如下的两个例子：

#### `s1 = aaxa`, `s2 = aac`####

这种情况下，下一步的优化后比较形式应该是这样的：

	s1: aaxa
	s2:  aac

`s1[2] = x` 应该与 `s2[1] = a` 比较，因为我们根据 `s2` 的自身已经可以确定 `s1[1] = s2[0] = a`。这样的话我们知道 `next[2] = 1`。

#### `s1 = abxa`, `s2 = abc`####

这种情况下优化好像没什么卵用，只能像之前一样比较 `s1[2]` 和 `s2[0]`，既如下所示：

	s1: abxa
	s2:   abc

这样的话我们就可以得出，在这类情况下，`next[2] = 0`。

这个时候，要计算跳转值，我们就要弄清跳转值在这里的作用是什么。假设 `next[m] = n`，那么首先我们可以知道，对于 `s1[i-m:i]` 和 `s2[:m]` 这 m 个 char 是完全一样的。接下来根据跳转值比较 `s1[i]` 和 `s2[n]`，也就是说 `s1[i-n:i]` 和 `s2[:n]` 这 n 个 char 也是完全一样的。

所以，其实对于求 m 的跳转值，就是找在 `s2[:m]` 之间最大的 n，使得 `s2[:n] == s2[m-n:m]`。假设我们已知 `next[m] = n`，如果 `s2[m] == s2[next[m]]`，就可以很顺理成章的推出 `s2[:n+1] == s2[m-n:m+1]`，即可得 `next[m+1] = n+1`。只有当不存在这个 n 满足以上条件时，我们才会得出 `next[m+1] = 0` 这个结果。

然后就会出现一种情况就是当 `s2[m] != s2[next[m]]` 的时候，我们应该怎么处理。假设 `next[m] = a`，我们已知的是 `s2[:a] == s2[m-a:m]`，这种情况下，假设 `next[m+1] = b, s2[:b] == s2[m-b+1:m+1]`，但是因为 `s2[m] != s2[a]`，所以 b 的最大值不会超过 a-1。

等一下，a-1 好像是一个很熟悉的 index，如果 `next[a] = x`，那么我们可以得出 `s2[:x] == s2[a-x:a]`。也就是说如果 `s2[m] == s2[x]`，我们就可以得出 `s2[:x+1] == s2[m-x:m+1]`，即 `next[m+1] = b = x+1`。同时也易知 x+1 就是 b 的最大值，因为 b 要是再大的话，`s2[m-b+1:m+1]` 就会包括 `s2[a]`，但是我们知道 `s2[a] != s2[m]`，这样就产生了矛盾。

到此，我们就可以得到生成 `next[]` 的函数：

	def get_next(s):
		next = [0 for _ in range(len(s))]
		next[0] = -1
		for i in range(2, len(s)):
			prev = i - 1
			while prev != -1:
				if s[i-1] == s[next[prev]]:
					next[i] = next[prev] + 1
					break
				else:
					prev = next[prev]
		return next
		
上面过程其实画个图就可以很直观的理解了，写出来还是有点绕的。


## 匹配字符串 ##

匹配的过程就没啥好说的，大概的伪代码我前面已经写了，当我们发现跳转值变成 -1 的时候，就可以移动 `s1` 的 index 了。具体的代码可见 [MyGitHub](https://github.com/starcroce/PyAlgoDataStructure/blob/master/string/strstr.py:)。

因为在 KMP 的情况下只用 `s1` 的 index 只用线性移动一遍，但是复杂度分析依然是另一回事，观察生成 `next[]` 的函数，这是个双层循环，但是其实复杂度还是 O(n) 的。具体的分析等我有空了再写一篇，这个涉及到 atmortized complexity。