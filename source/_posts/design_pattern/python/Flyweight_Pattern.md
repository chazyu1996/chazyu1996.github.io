---
title: "享元模式python实现"
tags:
  - python
  - 设计模式python实现
categories:
  - 设计模式
toc: true
date: 2019-11-25 22:41:46
---

# 享元模式

## 介绍

主要用于减少创建对象的数量，以减少内存占用和提高性能。这种类型的设计模式属于结构型模式，它提供了减少对象数量从而改善应用所需的对象结构的方式。

享元模式尝试重用现有的同类对象，如果未找到匹配的对象，则创建新对象
<!--more-->

## 应用场景：

-  数据库连接池
-  如果一个应用程序使用了大量的对象，而这些对象造成了很大的存储开销的时候就可以考虑是否可以使用享元模式。

## 代码实现

### 享元模式

```python
class FlyweightBase:
    def offer(self):
        """享元基类"""
        pass

class Flyweight(FlyweightBase):
    """共享享元类"""
    def __init__(self, name):
        self.name = name
    def get_price(self, price):
        print('产品类型：{} 详情：{}'.format(self.name, price))

class FactoryFlyweight:
    """享元工厂类"""
    def __init__(self):
        self.product = {}
    def Getproduct(self, key):
        if not self.product.get(key, None):
            self.product[key] = Flyweight(key)
        return self.product[key]

if __name__ == '__main__':
    test = FactoryFlyweight()
    A = test.Getproduct("高端")
    A.get_price("香水：80")
    B = test.Getproduct("高端")
    B.get_price("面膜：800")
"""
产品类型：高端 详情：香水：80
产品类型：高端 详情：面膜：800
"""
```

