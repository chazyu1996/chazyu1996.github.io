---
title: "适配器模式python实现"
tags:
  - python
  - 设计模式python实现
categories:
  - 设计模式
toc: true
date: 2019-11-25 22:41:46
---

# 适配器模式

## 介绍

将一个类的接口转换成客户希望的另外一个接口。适配器模式使得原本由于接口不兼容而不能一起工作的那些类可以一起工作。
<!--more-->

## 应用场景：

-  面对一个有全新接口的类库而又不能改变现有代码时，最先想到的做法是，在这两个系统之间添加一个适配器。
-  grpc 也可以认为是一种适配器，提供了跨语言调用能力
-  sqlalchemy 可以在不改变代码的情况下对接多种数据库

## 代码实现

### 适配器模式


```python
# 鸭子的简单描述
class Duck:
    def quack(self):
        # 会呱呱叫
        print("Quack")
    
    def fly(self):
        # 飞的能力
        print("I'm flying")
        
# 火鸡的简单描述
class Turkey:
    def gobble(self):
        # 不会呱呱叫，只会咯咯叫
        print("Gobble gobble")
    
    def fly(self):
        # 飞的能力 但是飞不远
        print("I'm flying a short distance")
    
```
因为现在没有鸭子对象，只能那火鸡对象冒充。由于鸭子对象和火鸡对象功能不同，不能直接拿来用，现在就需要使用适配器来完成这个功能
```python
class TurkeyAdapter(Duck):
    turkey = Turkey()  # 这里实际使用的是火鸡对象
    
    # 实现鸭子对象拥有的quack方法
    def quack(self):
        self.turkey.gobble()
    
    def fly(self):
        # 假设火鸡比鸭子飞的短，为了模拟鸭子的动作，多飞几次
        for i in range(5):
            turkey.fly()

# test

duck = Duck()
duck.quack()
duck.fly()

turkey_adapter = Duck()
turkey_adapter.quack()
turkey_adapter.fly()
```

