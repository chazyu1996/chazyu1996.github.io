---
title: "中介者模式python实现"
tags:
  - python
  - 设计模式python实现
categories:
  - 设计模式
date: 2019-07-05T10:03:48+08:00
toc: true
---

# 中介者模式

## 介绍

用来降低多个对象和类之间的通信复杂性。这种模式提供了一个中介类，该类通常处理不同类之间的通信，并支持松耦合，使代码易于维护。中介者模式属于行为型模式。
<!--more-->

## 应用场景：

-   系统中对象之间存在比较复杂的引用关系，导致它们之间的依赖关系结构混乱而且难以复用该对象。
-    想通过一个中间类来封装多个类中的行为，而又不想生成太多的子类。

## 代码实现

### 中介者模式


```python
class Consumer:
    """消费者类"""
    def __init__(self, product, price):
        self.name = "消费者"
        self.product = product
        self.price = price
    def shopping(self, name):
        """买东西"""
        print("向{} 购买 {}价格内的 {}产品".format(name, self.price, self.product))

class Producer:
    """生产者类"""
    def __init__(self, product, price):
        self.name = "生产者"
        self.product = product
        self.price = price
    def sale(self, name):
        """卖东西"""
        print("向{} 销售 {}价格的 {}产品".format(name, self.price, self.product))

class Mediator:
    """中介者类"""
    def __init__(self):
        self.name = "中介者"
        self.consumer = None
        self.producer = None
    def sale(self):
        """进货"""
        self.consumer.shopping(self.producer.name)
    def shopping(self):
        """出货"""
        self.producer.sale(self.consumer.name)
    def profit(self):
        """利润"""
        print('中介净赚：{}'.format((self.consumer.price - self.producer.price )))
    def complete(self):
        self.sale()
        self.shopping()
        self.profit()

if __name__ == '__main__':
    consumer = Consumer('手机', 3000)
    producer = Producer("手机", 2500)
    mediator = Mediator()
    mediator.consumer = consumer
    mediator.producer = producer
    mediator.complete()

# 向生产者 购买 3000价格内的 手机产品
# 向消费者 销售 2500价格的 手机产品
# 中介净赚：500
```

