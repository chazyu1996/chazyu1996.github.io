---
title: "策略模式python实现"
tags:
  - python
  - 设计模式python实现
categories:
  - 设计模式
date: 2019-07-05T10:03:48+08:00
toc: true
---

# 策略模式

## 介绍

就是能够把一系列“可互换的”算法封装起来，并根据用户需求来选择其中一种。
我们可以通过这种模式将一个父类中会变化的部分提取并封装起来，以便此后可以轻易地改变或者扩展这部分，而不影响其他部分。
<!--more-->

## 应用场景：

-   1、如果在一个系统里面有许多类，它们之间的区别仅在于它们的行为，那么使用策略模式可以动态地让一个对象在许多行为中选择一种行为。 
-   2、一个系统需要动态地在几种算法中选择一种。 
-   3、如果一个对象有很多的行为，如果不用恰当的模式，这些行为就只好使用多重的条件选择语句来实现。

## 代码实现

### 策略模式
模拟一款游戏，里面有各种各样的鸭子，这里我们只设计两种，一种是会飞的鸭子，另一种是会叫的鸭子。

在策略模式中，有几种不同的行为就定义几个不同的接口，这里我们把会飞、不会飞、会用羽毛飞等统一称为一种行为，即飞行行为；把会呱呱叫、会哇哇叫等统一称为另一种行为，即会叫的行为。

首先，定义第一种行为接口，飞行行为接口，飞行行为下的不同种类都实现这个接口，以表示不同的飞行行为：

```python
class FlyBehavior(object):
    """所有飞行行为都必须实现该类的fly方法"""
    def fly(self):
        raise NotImplementedError()

class FlyDuckWithWings(FlyBehavior):
    """会用羽毛飞的鸭子继承飞行行为类，实现飞行接口"""
    def fly(self):
        print("老子有羽毛，老子会飞，你们会么。2333333。")

class FlyDuckNoWay(FlyBehavior):
    """不会飞的鸭子，继承飞行行为类，实现飞行接口"""
    def fly(self):
        print("我不会飞，坐在角落里诅咒你们掉下来。")
```

接着定义鸭叫这个行为接口：

```python
class QuackBehavior(object):
    """呱呱叫的行为接口类"""
    def quack(self):
        raise NotImplementedError()

class DuckQuackGua(QuackBehavior):
    """只会呱呱叫的鸭子"""
    def quack(self):
        print("看见美鸭，我会呱呱叫。呱呱呱......")

class DuckQuackWa(object):
    """只会哇哇叫的鸭子"""
    def quack(self):
        print("看见帅鸭，我会哇哇叫。哇哇哇......")
```
这两种行为类定义完成，并有行为子类定义完成之后，就要定义鸭子类型了。
```python
class Duck(object):
    """鸭子对象"""
    # 为了简单说明，在这里直接放置两个参数，如果大家想要更好的实现，可以使用set方法进行设置
    def __init__(self, fly_duck, quack_duck):
        self.fly_duck = fly_duck
        self.quack_duck = quack_duck
    def fly(self):
        self.fly_duck.fly()
    def quack(self):
        self.quack_duck.quack()
```
测试
```python
if __name__ == '__main__':
    fly_gua_duck = Duck(FlyDuckWithWings(), DuckQuackGua())  # 创建一个会飞会呱的鸭子
    fly_gua_duck.fly()
    fly_gua_duck.quack()

    no_fly_wa_duck = Duck(FlyDuckNoWay(), DuckQuackWa())  # 创建一个不会飞，只会哇哇叫的鸭子
    no_fly_wa_duck.fly()
    no_fly_wa_duck.quack()
```

在上面，我们测试时，使用了类的实例。我们继续精简一下，使用静态方法。
```python
class FlyBehavior(object):
    """所有会飞的行为都必须继承该类，并实现其fly方法"""
    @staticmethod
    def fly():
        raise NotImplementedError()

class FlyDuckWithWings(FlyBehavior):
    """会飞的鸭子继承会飞行为类，实现飞行接口"""
    @staticmethod
    def fly():
        print("老子有羽毛，老子会飞，你们会么。2333333。")

class FlyDuckNoWay(FlyBehavior):
    """不会飞的鸭子，继承飞行行为类，实现飞行接口"""
    @staticmethod
    def fly():
        print("我不会飞，坐在角落里诅咒你们掉下来。")
```

这样，我们在测试的时候，传入类就可以了，无需实例出一个对象来。

好像这种方式也不是很好，接下来，我们使用函数来实现这种行为。

```python
class Duck(object):
    """鸭子对象"""
    def __init__(self, fly_duck, quack_duck):
        self.fly_duck = fly_duck
        self.quack_duck = quack_duck

def fly_with_wings():
    print("老子有羽毛，老子会飞，你们会么。2333333。")

def fly_no_way():
    print("我不会飞，坐在角落里诅咒你们掉下来。")

def quack_gua():
    print("看见美鸭，我会呱呱叫。呱呱呱......")

def quack_wa():
    print("看见帅鸭，我会哇哇叫。哇哇哇......")

if __name__ == '__main__':
    fly_gua_duck = Duck(fly_with_wings(), quack_gua())  # 创建一个会飞会呱的鸭子
    fly_gua_duck.fly
    fly_gua_duck.quack

    no_fly_wa_duck = Duck(fly_no_way(), quack_wa())
    no_fly_wa_duck.fly
    no_fly_wa_duck.quack
```
继续精简。
```python
class Duck(object):
    """鸭子对象"""

    def __init__(self, fly_duck, quack_duck):
        self.fly_duck = fly_duck
        self.quack_duck = quack_duck

def fly_with_wings():
    print("老子有羽毛，老子会飞，你们会么。2333333。")

def fly_no_way():
    print("我不会飞，坐在角落里诅咒你们掉下来。")

def quack_gua():
    print("看见美鸭，我会呱呱叫。呱呱呱......")

def quack_wa():
    print("看见帅鸭，我会哇哇叫。哇哇哇......")

if __name__ == '__main__':
    fly_gua_duck = Duck(fly_with_wings, quack_gua)  # 创建一个会飞会呱的鸭子
    fly_gua_duck.fly_duck()
    fly_gua_duck.quack_duck()
    no_fly_wa_duck = Duck(fly_no_way, quack_wa)
    no_fly_wa_duck.fly_duck()
    no_fly_wa_duck.quack_duck()
```