---
title: "责任链模式python实现"
tags:
  - python
  - 设计模式python实现
categories:
  - 设计模式
toc: true
date: 2019-11-25 22:41:46
---

# 责任链模式

## 介绍

为请求创建了一个接收者对象的链。这种模式给予请求的类型，对请求的发送者和接收者进行解耦。这种类型的设计模式属于行为型模式。
<!--more-->

## 应用场景：

-  将多个处理方法连接成一条链条，请求将在这条链条上流动直到该链条中有一个节点可以处理该请求；通常这条链条是一个对象包含对另一个对象的引用而形成链条，每个节点有对请求的条件，当不满足条件将传递给下一个节点处理。

## 代码实现

### 责任链模式


```python
class Bases:
    def __init__(self, obj=None):
        self.obj = obj
    def screen(self, number):
        pass

class A(Bases):
    def screen(self, number):
        if 200 > number > 100:
            print("{} 划入A集合".format(number))
        else:
            self.obj.screen(number)

class B(Bases):
    def screen(self, number):
        if number >= 200:
            print("{} 划入B集合".format(number))
        else:
            self.obj.screen(number)

class C(Bases):
    def screen(self, number):
        if 100 >= number:
            print("{} 划入C集合".format(number))

if __name__ == '__main__':
    test = [10, 100, 150, 200, 300]
    c = C()
    b = B(c)
    a = A(b)
    for i in test:
        a.screen(i)

"""
-------------------
10 划入C集合
100 划入C集合
150 划入A集合
200 划入B集合
300 划入B集合
"""
```

## 注意：

- 一个对象中含有另一个对象的引用以此类推形成链条
- 每个对象中应该有明确的责任划分即处理请求的条件
- 链条的最后一节应该设计成通用请求处理，以免出现漏洞
- 请求应该传入链条的头部