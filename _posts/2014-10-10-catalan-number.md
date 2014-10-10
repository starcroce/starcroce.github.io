---
layout: post
title: "Catalan Number"
description: ""
category: life
tags: [Algorithm, Maths, LeetCode]
---
{% include JB/setup %}

重刷 LeetCode 的时候碰到了那道 Unique Binary Search Tree，虽然知道答案就是 Catalan Number，但是一直没弄清到底怎么回事，趁着我的程序在 server 上估计还要跑 10 个小时才能出结果，就顺便研究下这个。

通项公式没什么好说的，如下所示：

![](http://upload.wikimedia.org/math/d/1/1/d118d8cea7b639dfd5244fcba65910cf.png)

wiki 表示递推公式有两种：

![](http://upload.wikimedia.org/math/6/2/1/6217b3c99a3243afcd5d8dbd58186822.png)

![](http://upload.wikimedia.org/math/8/a/4/8a49332e4a46b3a2c7accec81160f5e3.png)

鉴于 markdown 不方便写公式，我就不演示推导过程了（数学渣不要找借口！！（逃

### 应用

#### Dyck Word

Cn 是长度为 2n 的 dyck word 数目。Dyck word 是指一个有 n 个 X 和 n 个 Y 的字符串，所有前缀字符串都要满足 X 的数目大于等于 Y 的数目。比如 **XXXYYXYY** 就是一个 dyck word。

看到这里又想起 LeetCode 有一道求所有可能的括号排序 Generate Parentheses，跟 dyck word 一样不过就是前缀串里面左括号要大于等于右括号。以前一直是 DFS 求解的，要是知道这个在面试时候牛逼闪闪的说 不就是 catalan number 么。。。算了 no zuo no die，还是别给自己挖坑了。

#### Unique Binary Tree
Cn 表示 n 个节点组成不同构的 binary tree 或者 2n+1 个节点组成不同构的 full binary tree 的数目。其实这两者是一样的，因为要补满一个 binary tree 我们总需要 n+1 个节点。于是再次从 wiki 盗图：

![](http://upload.wikimedia.org/wikipedia/commons/0/01/Catalan_number_binary_tree_example.png)

然后这个怎么跟 catalan number 在一起搞基了呢。。我们先拿一个节点当做 root，于是剩下 n-1 个节点，这样左右 subtree 的节点数目就变成了 0: n-1, 1: n-2, ..., n-2: 1, n-1: 0。假设由 n 个节点组成的不同结构的 binary tree 数目是 T(n)。于是有：

T(n) = T(0) * T(n-1) + T(1) * T(n-2) + ... + T(n-2) * T(1) + T(n-1) * T(0)

有木有觉得眼熟啊。。递推公式1啊啊啊。。。 好的问题解决，套公式吧骚年们。。

#### 多边形划分
Cn表示通过连结顶点而将 n+2 边的凸多边形分成三角形的方法个数。解题过程基本跟上面类似，选取一个点出发划成两块，两个各有 x 和 y 条边，x+y=n+2。然后 x: y 的可能性有 2: n, 3: n-1, ..., n-1: 3, n: 2。

显而易见，跟 unique binary tree 一个路数，就不多加解释了。

#### 其他
很多问题可以归到上面两类中，比如入栈出栈，街区对角线等。总的来说我的理解就是如果问题的 solution 的形式是类似 dyck word 的，也就是一个序列，要求任意前缀子序列中元素 A 的数目大于等于元素 B，比如括号配对，入栈出栈顺序，街区行走路线等，就可以用 catalan number。

另一种就是类似 unique binary tree 的，除了上述两类，还有一个典型题 n 层阶梯切割问题。大概形式就是可以分割成两个子问题相加的形式，然后可以套用递推公式1来解决的。

### 代码实现
上面提供了通项公式和递推公式，那么在代码实现环节，我个人还是推荐递推公式。原因很简单，通项公式需要用到阶乘，这样的话除非是用 Python 等对大数运算支持的很好的语言，不然像 C++，Java 等都会遇到溢出的问题，哪怕是用 unsigned long long，也会有溢出的时候，除非自己实现无限大的大数运算。

以下是通项公式和递推公式的 Python 代码实现：

	import math
	
	def catalan_number_1(n):
		return math.factorial(2*n) / (math.factorial(n+1) * math.factorial(n))
	
	def catalan_number_2(n):
		res = [1, 1]
		for i in range(2, n+1):
			res.append(0)
			for j in range(0, i):
				res[i] += res[j] * res[i-j-1]
		return res[n]
	
	def main():
		for i in range(0, 10):
			print catalan_number_1(i), catalan_number_2(i)
	
	if __name__ == '__main__':
		main()

无视掉第二种丑陋的写法，明白怎么实现就好，话说谁告诉我怎么在 Mou 里面实现语法高亮。。。

### 最后
其实没什么想说的，吐槽下我的程序在 server 上都跑了40个小时还没好。刚刚问了下老板才知道给我 ec2 instance 是 micro 类型的，坑爹呢这是！！！早知道这样还不如用我自己机子跑呢。。

完。