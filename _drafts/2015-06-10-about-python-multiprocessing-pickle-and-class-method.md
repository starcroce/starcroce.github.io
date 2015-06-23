---
layout: post
title: "About Python multiprocessing, pickle and class method"
description: ""
category: work
tags: [Python]
---
{% include JB/setup %}

一个简单的例子，我们有两个 class 和一个 test function 如下所示：

    class A:
        def func(self):
            print 'func_A'

    class B:
        def __init__(self, name):
            if name == 'A':
                self.obj = A()
                self.func = self.obj.func

    def test(obj):
        obj.func()
        
那么对于`x1 = A()`和`x2 = B('A')`，在普通情况下运行`test(x1)`和`text(x2)`应该是一样的效果，都会在 terminal 里输出 func_A 。

不过如果涉及 multiprocessing 的话，情况就会有很大的不同，如果我们的 main function 如下所示：

    def main():
        from multiprocessing import Pool 
        x = A()

        pool = Pool()
        pool.apply(test, (x,))
        pool.close()
        pool.join()

很好，一切正常，我们在 termial 上看到了 func_A 。但是，如果把`x = A()`换成`x = B('A')`，就会报如下的错误：

    cPickle.PicklingError: Can't pickle <type 'instancemethod'>: attribute lookup __builtin__.instancemethod failed

具体