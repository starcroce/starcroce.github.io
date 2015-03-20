---
layout: post
title: "Check Duplicate Element in Array"
description: ""
category: life
tags: [Algorithm, Maths]
---
{% include JB/setup %}

随手看到的一道题，对于一个 size 为 n 的数组，里面每个元素的值都在 0 到 n-1 之间，返回这个数组中是否有重复元素。

用 hash table 的解法就不说了，想一下有没有 O(1) 的额外空间复杂度，然后觉得跟 LeetCode 的一道 first missing positive 很像。大概的做法就是如下：

1. if A[x] == x, move forward, tmp = -1
2. if A[x] = y, swap tmp and A[x], so A[x] = -1, tmp = y, check A[y]
3. if A[y] == y, there is a duplicate element
4. if A[y] = z, swap tmp and A[y], so A[y] = y, tmp = z, check A[z]
5. same step as above ...

这样的话，对于 `A[x_0] = x_1, A[x_1] = x_2, A[x_3] = x_4, ..., A[x_n-1] = x_n, A[x_n] = x_0` 的一组元素，经过 n 次 swap 之后，就会满足 `A[i] = i`。

如果整个数组没有重复元素，那么必定会在 n 次 swap 之后使得任意 `A[i] = i`，这样的话就可以直接返回 `false`。先上代码：

	#include <algorithm>
	#include <iostream>
	using namespace std;

	bool hasDuplicate(int A[], int n)
	{
    	int count = 0, pos = 0, tmp = -1;
    	while (count < n)
    	{
        	while (A[pos] == pos)
        	{
            	pos++;
        	}
        	if (pos >= n)
        	{
            	break;
        	}
        	if (tmp == -1)
        	{
            	swap(tmp, A[pos]);
        	}
        	else if (tmp == A[tmp])
        	{
            	return true;
        	}
        	else
        	{
            	swap(tmp, A[tmp]);
        	}
    	}
    	return false;
	}

	int main(int argc, char const *argv[])
	{
    	int A[] = {1, 2, 3, 1};
    	cout<<hasDuplicate(A, 4)<<endl;
    	return 0;
	}

内循环保证了 `pos` 永远指向第一个 `A[i] != i` 的位置，对于没有重复元素的情况，之前已经分析过会保证所有 `A[i] = i`，从而使得 `pos = n`，退出循环，返回 `false`。

对于存在重复元素，内循环每次 `pos++` 也保证了我们一定会碰到 `tmp = A[tmp]`，即使不在 swap 的过程中遇到，跳出内循环之后也会遇到这个情况。

复杂度分析的话，对于 n 个 `A[i] != i`，需要 n 次 swap 操作使得 `A[i] = i`，如果 array 里有 m 个 `A[i] = i` 以及 n 个 `A[i] != i`，时间复杂度应该就是 O(m + 2n)。