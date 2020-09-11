---
title: "迭代器模式python实现"
tags:
  - python
  - 设计模式python实现
categories:
  - 设计模式
date: 2019-07-05T10:03:48+08:00
toc: true
---

# 迭代器模式

## 介绍

外提供一个接口，实现顺序访问聚合数据，但是不显示该数据的内部机制。这就是Python中大名鼎鼎的迭代器。
<!--more-->

实现迭代模式对于Python来说没有多余的代码，寥寥几行代码足可以实现迭代模式。
## 应用场景：

-  如果一种特定类型的问题发生的频率足够高，那么可能就值得将该问题的各个实例表述为一个简单语言中的句子。这样就可以构建一个解释器，该解释器通过解释这些句子来解决该问题。

## 代码实现

### 迭代器模式

```python
def FibonacciSequence(n):
    x = 0
    y = 1
    i = 1
    while True:
        yield y
        if i == n:
            break
        x, y = y, x+y
        i += 1

if __name__ == '__main__':
    test = FibonacciSequence(7)
    next(test)
    # 1
    next(test)
    # 1
    next(test)
    # 2
    next(test)
    # 3
    next(test)
    # 5
    next(test)
    # 8
    next(test)
    # 13
    next(test)
```

以上是使用迭代模式输出斐波那契数列的前n列，较传统的实现方法而言更加的简洁。

迭代器模式常应用场景是在只提供接口而不暴露内部机制的场景中，yield关键词在python协程中也有应用。

迭代器、生成器、可迭代对象概念

生成器：对于一个数据集合，生成器并不记住每个元素值，但在循环中记录元素位置并根据元素生成规则推算出数值，这种边循环边计算的形式是生成器。

迭代器：是一种访问集合的方式，记住遍历位置，从第一个元素开始访问，直到最后一个元素，并且只能前进不能后退。

可迭代对象：像list、set、str这种可以通过for遍历的类型是可迭代对象，这种遍历顺序可以从尾到头。

凡是通过next（）访问的对象都是迭代器类型，也就是说生成器就是迭代器的一种；凡是可以通过for遍历的都是可迭代对象，可迭代对象可以通过iter（）转化为迭代器。

生成器中有几个关键词：yield、yield form、send、next()、__next__()具体作用见示

```python
def test():
    a = 1
    while True:
        b = yield a
        a += b

def test1():
    yield from test()   # yield form 是创建一个嵌套的生成器，form后面跟一个生成器，每次执行到yield form后会先把内层的生成器执行完。


if __name__ == '__main__':
    fn = test()
    next(fn)    # 通过next访问内部元素
    fn.__next__()   # 通过__next__()方法访问内部元素，作用同上
    fn.send(4)  # send有next的作用，同时向生成器内部的yield左边等式赋值

    fn1 = test1()
    fn.__next__()
```

> 迭代器和可迭代对象有几个关键词：next（）、itre（）、for

```python
# 迭代器及可迭代对象

a = (i for i in range(50))
b = [1, 2, 3, 4, 5, 6]

if __name__ == '__main__':
    next(a)
    c = iter(b)
    next(c)
```