---
layout: post
title: "How Many Primes Less Than 100 Million"
description: ""
category: life
tags: [Algorithm, Maths, C++]
---
{% include JB/setup %}

偶然看到一道题目求一亿以内的质数，然后想起来我好像被这种题目坑了好几次= =。。。真是不长记性。。。索性这次就一撸到底想想清楚怎么求质数。。。

### 传统解法

按照小学奥赛的传统解法，判断 x 是不是质数的方法就是 x 不能被从 2 到 x-1 的所有整数整除，显而易见这么做的话复杂度上限好像是 O(n^2)？，嗯无法直视。。。不过真的是这样吗？

然后稍微做下改进，为什么要 2 到 x-1 呢？明明 2 到 sqrt(x) 就可以了嘛。。。但很容易发现，这个改进其实有没有都一样，因为如果 x 是合数，那么我们肯定会在 2 到 sqrt(x) 之间的某个数上跳出判断是否为质数的循环，所有传统解法看似复杂度 O(n^2)，但是实际应用中好像会退化到 O(n^1.5)。。。不过，还是那个问题，真的是这样吗？

把传统解法压榨到极限，那么就是对于 x，我们检查从 2 到 sqrt(x) 之间的所有质数，看上去好像很有道理的样子，好像可以省了好多计算。。。我们知道一般来说，对于不大于 x 的质数个数，大概可以近似成 x / lnx，于是这个改进的复杂度就变成了 O(n * sqrt(n) / ln(sqrt(n))) = O(n^1.5 / (2ln n))。。。虽然不知道我写的是什么，但是看上去还是很厉害的样子。。。可是还是一样的问题，不管用哪种改进，break 的那次肯定是 2 到 sqrt(n) 之间的某个质数。。。所以我觉得所有传统解法的复杂度最后都会退化成 O(n^1.5 / (2ln n))，不过常数项上肯定有差距。。。

嗯以上都是我瞎算的，数学不行编不下去了。。。

给个 cpp 实现吧

    int getPrimes(int n) {
        vector<int> primeNum;
        primeNum.push_back(2);
        for(int i = 3; i <= n; i++) {
            int threshold = (int)sqrt(i) + 1;
            int j = 0;
            bool flag = true;
            while(primeNum[j] <= threshold) {
                if(i % primeNum[j] == 0) {
                    flag = false;
                    break;
                }
                j++;
            }
            if(flag == true) {
                primeNum.push_back(i);
            }
        }
        return primeNum.size();
    }

我知道肯定有代码层的优化，目前就这么着吧。。。

### Eratosthenes 筛法

大概方法就是列一个 2~n 的表，从 3 开始，先标记 2 的倍数，剩下第一个是 3，然后标记 3 的倍数，剩下第一个是 5，再标记 5 的倍数。。。以此类推，表上剩下的就是小于 n 的质数了。。。据说复杂度是 O(nloglogn)，看上去好高大上的样子。。。以下是 cpp 实现：

    int getPrimes_Eratosthenes(int num) {
        vector<bool> isPrime(num+1, true);
        isPrime[0] = false; isPrime[1] = false;
        for(int i = 2; i <= num; i++) {
            if(isPrime[i] == true) {
                for(int j = i * 2; j <= num; j += i) {
                    isPrime[j] = false;
                }
            }
        }
        int res = 0;
        for(int i = 2; i <= num; i++) {
            if(isPrime[i] == true) {
                res++;
            }
        }
        return res;
    }
    
看上去很理想的样子，但为什么复杂度不是 O(n) 呢？很简单，因为并不是每个数都只处理了一遍，比如 6，在 2 的倍数那轮标记了一下，在 3 的倍数那轮又被标记了一下。。。也就是说，对于每个质数 p，都要做 n/p 次操作，对于小于 n 的所有质数的倒数和，这个值是 loglogn。。。证明方法可以直接看 [wiki](http://en.wikipedia.org/wiki/Divergence_of_the_sum_of_the_reciprocals_of_the_primes)，大神也可以自己验证下。。。
    
### Euler 筛法

嗯听名字就知道是很给力的解法。。。当年被 Euler 虐的死去活来的。。。先上代码吧。。。

    int getPrimes_Euler(int num) {
        vector<bool> isPrime(num+1, false);
        vector<int> prevPrimes;
        for(int i = 2; i <= num; i++) {
            if(isPrime[i] == false) {
                prevPrimes.push_back(i);
            }
            for(int j = 0; j < prevPrimes.size(); j++) {
                if(i * prevPrimes[j] > num) {
                    break;
                }
                isPrime[i * prevPrimes[j]] = true;
                if(i % prevPrimes[j] == 0) {
                    break;
                }
            }
        }
        return prevPrimes.size();
    }

Euler 筛法是 O(n) 的，因为每个数都被它最小的质因子标记且只标记一次，代码中`if(i % prevPrimes[j] == 0)`这里保证了这一点。。。

假设合数 n 的最小质因数为 p，n = pm = p'm', (p < p', m > m')，当`prevPrmes[j] == p`时， n 被标记，接下来执行下面的`if`语句，直接跳出循环，所以 p' 不会被遍历到，从而保证每个数只会被标记一次，因为复杂度为 O(n)。。。

### 运行时间分析

硬件环境：

    Model Name:         MacBook Pro
    Model Identifier:   MacBookPro11,1
    Processor Name:     Intel Core i5
    Processor Speed:    2.4 GHz
    Number of Processors:   1
    Total Number of Cores:  2
    L2 Cache (per Core):    256 KB
    L3 Cache:   3 MB
    Memory:     8 GB

编译环境：

    ➜  ~  g++ -v
    Configured with: --prefix=/Applications/Xcode.app/Contents/Developer/usr --with-gxx-include-dir=/usr/include/c++/4.2.1
    Apple LLVM version 6.0 (clang-600.0.54) (based on LLVM 3.5svn)
    Target: x86_64-apple-darwin14.0.0
    Thread model: posix
    
全部代码：

    #include <iostream>
    #include <vector>
    #include <cmath>
    #include <ctime>
    using namespace std;

    int getPrimes(int n) {
        vector<int> primeNum;
        primeNum.push_back(2);
        for(int i = 3; i <= n; i++) {
            int threshold = (int)sqrt(i);
            int j = 0;
            bool flag = true;
            while(primeNum[j] <= threshold) {
                if(i % primeNum[j] == 0) {
                    flag = false;
                    break;
                }
                j++;
            }
            if(flag == true) {
                primeNum.push_back(i);
            }
        }
        return primeNum.size();
    }

    int getPrimes_Eratosthenes(int num) {
        vector<bool> isPrime(num+1, true);
        isPrime[0] = false; isPrime[1] = false;
        for(int i = 2; i <= num; i++) {
            if(isPrime[i] == true) {
                for(int j = i * 2; j <= num; j += i) {
                    isPrime[j] = false;
                }
            }
        }
        int res = 0;
        for(int i = 2; i <= num; i++) {
            if(isPrime[i] == true) {
                res++;
            }
        }
        return res;
    }

    int getPrimes_Euler(int num) {
        vector<bool> isPrime(num+1, false);
        vector<int> prevPrimes;
        for(int i = 2; i <= num; i++) {
            if(isPrime[i] == false) {
                prevPrimes.push_back(i);
            }
            for(int j = 0; j < prevPrimes.size(); j++) {
                if(i * prevPrimes[j] > num) {
                    break;
                }
                isPrime[i * prevPrimes[j]] = true;
                if(i % prevPrimes[j] == 0) {
                    break;
                }
            }
        }
        return prevPrimes.size();
    }

    int main(int argc, char const *argv[])
    {
        int num    = 10000000;
        clock_t t0 = clock();
        int res1   = getPrimes(num);
        clock_t t1 = clock();
        int res2   = getPrimes_Eratosthenes(num);
        clock_t t2 = clock();
        int res3   = getPrimes_Euler(num);
        clock_t t3 = clock();
        cout<<res1<<' '<<res2<<' '<<res3<<endl;
        cout<<(double)(t1-t0)/CLOCKS_PER_SEC<<' ';
        cout<<(double)(t2-t1)/CLOCKS_PER_SEC<<' ';
        cout<<(double)(t3-t2)/CLOCKS_PER_SEC<<endl;
        return 0;
    }
    
g++ -O1, -O2, -O3 每种各实验10次，取平均值。

Parameter | O1 | O2 | O3 |
:---------------|:---------------|:---------------|
Traditional | 1.859249 | 1.058514 | 1.051012 |
Eratosthenes | 0.7223996 | 0.0754521 | 0.0752899 |
Euler | 0.4069224 | 0.0837832 | 0.0859442 |

嗯非常有趣的结果。。。不清楚编译器怎么优化的。。。这个目前已经超出我的水平了。。。