---
title: "装饰器模式python实现"
tags:
  - python
  - 设计模式python实现
categories:
  - 设计模式
toc: true
date: 2019-11-25 22:41:46
---

# 装饰器模式

## 介绍

允许向一个现有的对象添加新的功能，同时又不改变其结构。这种类型的设计模式属于结构型模式，它是作为现有的类的一个包装。
<!--more-->

## 应用场景：

-  当用于实现横切关注点
-  数据校验
-  事务处理（这里的事务类似于数据库事务，意味着要么所有步骤都成功完成，要么事务失败） 缓存
-  日志
-  监控
-  调试
-  业务规则
-  压缩
-  加密

## 代码实现

### 装饰器模式

修饰器模式和Python修饰器之间并不是一对一的等价关系。Python修饰器能做的实际上比修饰器模式多得多，其中之一就是实现修饰器模式


```python
from functools import wraps


def makebold(fn):
    return getwrapped(fn, "b")


def makeitalic(fn):
    return getwrapped(fn, "i")


def getwrapped(fn, tag):
    @wraps(fn)
    def wrapped():
        return "<%s>%s</%s>" % (tag, fn(), tag)
    return wrapped


@makebold
@makeitalic
def hello():
    """a decorated hello world"""
    return "hello world"

if __name__ == '__main__':
    print('result:{}   name:{}   doc:{}'.format(hello(), hello.__name__, hello.__doc__))

### OUTPUT ###
# result:<b><i>hello world</i></b>   name:hello   doc:a decorated hello world
```

```python
class foo(object):
    def f1(self):
        print("original f1")

    def f2(self):
        print("original f2")


class foo_decorator(object):
    def __init__(self, decoratee):
        self._decoratee = decoratee

    def f1(self):
        print("decorated f1")
        self._decoratee.f1()

    def __getattr__(self, name):
        return getattr(self._decoratee, name) # 这个不是delegation么

u = foo()
v = foo_decorator(u)
v.f1()
v.f2()

#decorated f1
#original f1
#original f2
```
