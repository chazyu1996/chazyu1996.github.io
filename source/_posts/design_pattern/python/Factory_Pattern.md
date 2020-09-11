---
title: "工厂模式python实现"
tags:
  - python
  - 设计模式python实现
categories:
  - 设计模式
toc: true
date: 2019-11-25 22:41:46
---
# 工厂模式

## 介绍

通过一个指定的“工厂”获得需要的“产品”，在设计模式中主要用于抽象对象的创建过程，让用户可以指定自己想要的对象而不必关心对象的实例化过程。这样做的好处是用户只需通过固定的接口而不是直接去调用类的实例化方法来获得一个对象的实例，隐藏了实例创建过程的复杂度，解耦了生产实例和使用实例的代码，降低了维护的复杂性。
<!--more-->

## 应用场景：

简单工厂模式适用于需创建的对象较少，不会造成工厂方法中的业务逻辑太过复杂的情况下，而且用户只关心那种类型的实例被创建，并不关心其初始化过程时，比如多种数据库(MySQL/MongoDB)的实例，多种格式文件的解析器(XML/JSON)等。

工厂方法模式继承了简单工厂模式的优点又有所改进，其不再通过一个工厂类来负责所有产品的创建，而是将具体创建工作交给相应的子类去做，这使得工厂方法模式可以允许系统能够更高效的扩展。实际应用中可以用来实现系统的日志系统等，比如具体的程序运行日志，网络日志，数据库日志等都可以用具体的工厂类来创建。

抽象工厂模式在工厂方法基础上扩展了工厂对多个产品创建的支持，更适合一些大型系统，比如系统中有多于一个的产品族，且这些产品族类的产品需实现同样的接口，像很多软件系统界面中不同主题下不同的按钮、文本框、字体等等。


## 代码实现

### 简单工厂
```python
class Mercedes(object):
    """梅赛德斯
    """
    def __repr__(self):
        return "Mercedes-Benz"

class BMW(object):
    """宝马
    """
    def __repr__(self):
        return "BMW"
# ====================
mercedes = Mercedes()
bmw = BMW()
```


### 工厂方法


虽然有了一个简单的工厂，但在实际使用工厂的过程中，我们会发现新问题：如果我们要新增一个“产品”，例如`Audi`的汽车，我们除了新增一个`Audi`类外还要修改`SimpleCarFactory`内的`product_car`方法。这样就违背了软件设计中的开闭原则，即在扩展新的类时，尽量不要修改原有代码。所以我们在简单工厂的基础上把`SimpleCarFactory`抽象成不同的工厂，每个工厂对应生成自己的产品，这就是工厂方法。
```python
import abc

class AbstractFactory(object):
    """抽象工厂
    """
    __metaclass__ = abc.ABCMeta

    @abc.abstractmethod
    def product_car(self):
        pass

class MercedesFactory(AbstractFactory):
    """梅赛德斯工厂
    """
    def product_car(self):
        return Mercedes()

class BMWFactory(AbstractFactory):
    """宝马工厂
    """
    def product_car(self):
        return BMW()
# ==================
c1 = MercedesFactory().product_car()
c2 = BMWFactory().product_car()
```

每个工厂负责生产自己的产品也避免了我们在新增产品时需要修改工厂的代码，而只要增加相应的工厂即可。如新增一个Audi产品，只需新增一个**Mouse**类和**MouseFactory**

### 抽象工厂

```python
#coding=utf-8
import abc

# 两种小汽车
class Mercedes_C63(object):
    """梅赛德斯 C63
    """
    def __repr__(self):
        return "Mercedes-Benz: C63"

class BMW_M3(object):
    """宝马 M3
    """
    def __repr__(self):
        return "BMW: M3"

#　两种SUV
class Mercedes_G63(object):
    """梅赛德斯 G63
    """
    def __repr__(self):
        return "Mercedes-Benz: G63"

class BMW_X5(object):
    """宝马 X5
    """
    def __repr__(self):
        return "BMW: X5"

class AbstractFactory(object):
    """抽象工厂
    可以生产小汽车外，还可以生产SUV
    """
    __metaclass__ = abc.ABCMeta

    @abc.abstractmethod
    def product_car(self):
        pass

    @abc.abstractmethod
    def product_suv(self):
        pass

class MercedesFactory(AbstractFactory):
    """梅赛德斯工厂
    """
    def product_car(self):
        return Mercedes_C63()

    def product_suv(self):
        return Mercedes_G63()

class BMWFactory(AbstractFactory):
    """宝马工厂
    """
    def product_car(self):
        return BMW_M3()

    def product_suv(self):
        return BMW_X5()
# ====================
c1 = MercedesFactory().product_car()
s1 = MercedesFactory().product_suv()
print(c1, s1)
s2 = BMWFactory().product_suv()
c2 = BMWFactory().product_car()
print(c2, s2)
```
