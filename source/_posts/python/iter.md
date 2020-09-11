---
title: python的生成器和迭代器
date: 2019-07-05T10:03:48+08:00
toc: true
tags:
- python
categories:
  - 技术文章

---

# 迭代器与生成器

## 可迭代对象(iterable)

很多容器（list、tuple、dict等）都是可迭代对象，此外还有更多的对象同样也是可迭代对象，比如处于打开状态的files，sockets等等。但凡是可以返回一个迭代器的对象都可称之为可迭代对象
<!--more-->

```
>>> x = [1, 2, 3]
>>> y = iter(x)
>>> z = iter(x)
>>> next(y)
1
>>> next(y)
2
>>> next(z)
1
>>> type(x)
<class 'list'>
>>> type(y)
<class 'list_iterator'>
```
这里x是一个可迭代对象，可迭代对象和容器一样是一种通俗的叫法，并不是指某种具体的数据类型，list是可迭代对象，dict是可迭代对象，set也是可迭代对象。y和z是两个独立的迭代器，迭代器内部持有一个状态，该状态用于记录当前迭代所在的位置，以方便下次迭代的时候获取正确的元素。迭代器有一种具体的迭代器类型，比如list_iterator，set_iterator。可迭代对象实现了__iter__方法，该方法返回一个迭代器对象。

![](/Users/yuzichen/blog/source/images/python/iterable-vs-iterator_1.png)

## 迭代器(iterator)

那么什么迭代器呢？它是一个带状态的对象，他能在你调用next()方法的时候返回容器中的下一个值，任何实现了__iter__和__next__()（python2中实现next()）方法的对象都是迭代器，__iter__返回迭代器自身，__next__返回容器中的下一个值，如果容器中没有更多元素了，则抛出StopIteration异常，至于它们到底是如何实现的这并不重要。

所以，迭代器就是实现了工厂模式的对象，它在你每次你询问要下一个值的时候给你返回。有很多关于迭代器的例子，比如itertools函数返回的都是迭代器对象。

生成无限序列：
```
>>> from itertools import count
>>> counter = count(start=13)
>>> next(counter)
13
>>> next(counter)
14
```
从一个有限序列中生成无限序列：
```
>>> from itertools import cycle
>>> colors = cycle(['red', 'white', 'blue'])
>>> next(colors)
'red'
>>> next(colors)
'white'
>>> next(colors)
'blue'
>>> next(colors)
'red'
```
从无限的序列中生成有限序列：
```
>>> from itertools import islice
>>> colors = cycle(['red', 'white', 'blue'])  # infinite
>>> limited = islice(colors, 0, 4)            # finite
>>> for x in limited:                         
...     print(x)
red
white
blue
red
```

为了更直观地感受迭代器内部的执行过程，我们自定义一个迭代器，以斐波那契数列为例：
```
class Fib:
    def __init__(self):
        self.prev = 0
        self.curr = 1

    def __iter__(self):
        return self

    def __next__(self):
        value = self.curr
        self.curr += self.prev
        self.prev = value
        return value

>>> f = Fib()
>>> list(islice(f, 0, 10))
[1, 1, 2, 3, 5, 8, 13, 21, 34, 55]
```

Fib既是一个可迭代对象（因为它实现了__iter__方法），又是一个迭代器（因为实现了__next__方法）。实例变量prev和curr用户维护迭代器内部的状态。每次调用next()方法的时候做两件事：

为下一次调用next()方法修改状态
为当前这次调用生成返回结果
迭代器就像一个懒加载的工厂，等到有人需要的时候才给它生成值返回，没调用的时候就处于休眠状态等待下一次调用。

## 生成器(generator)

生成器算得上是Python语言中最吸引人的特性之一，生成器其实是一种特殊的迭代器，不过这种迭代器更加优雅。它不需要再像上面的类一样写__iter__()和__next__()方法了，只需要一个yiled关键字。 生成器一定是迭代器（反之不成立），因此任何生成器也是以一种懒加载的模式生成值。用生成器来实现斐波那契数列的例子是：

```
def fib():
    prev, curr = 0, 1
    while True:
        yield curr
        prev, curr = curr, curr + prev

>>> f = fib()
>>> list(islice(f, 0, 10))
[1, 1, 2, 3, 5, 8, 13, 21, 34, 55]
```
`fib`就是一个普通的python函数，它特殊的地方在于函数体中没有return关键字，函数的返回值是一个生成器对象。当执行`f=fib()`返回的是一个生成器对象，此时函数体中的代码并不会执行，只有显示或隐示地调用next的时候才会真正执行里面的代码。

生成器在Python中是一个非常强大的编程结构，可以用更少地中间变量写流式代码，此外，相比其它容器对象它更能节省内存和CPU，当然它可以用更少的代码来实现相似的功能。现在就可以动手重构你的代码了，但凡看到类似：

```
def something():
    result = []
    for ... in ...:
        result.append(x)
    return result
```

都可以用生成器函数来替换：
```
def iter_something():
    for ... in ...:
        yield x
```

## 总结
- 容器是一系列元素的集合，str、list、set、dict、file、sockets对象都可以看作是容器，容器都可以被迭代（用在for，while等语句中），因此他们被称为可迭代对象。
- 可迭代对象实现了__iter__方法，该方法返回一个迭代器对象。
- 迭代器持有一个内部状态的字段，用于记录下次迭代返回值，它实现了__next__和__iter__方法，迭代器不会一次性把所有元素加载到内存，而是需要的时候才生成返回结果。
- 生成器是一种特殊的迭代器，它的返回值不是通过return而是用yield。