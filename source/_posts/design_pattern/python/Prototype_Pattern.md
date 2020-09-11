---
title: "原型模式python实现"
tags:
  - python
  - 设计模式python实现
categories:
  - 设计模式
date: 2019-07-05T10:03:48+08:00
toc: true
---

# 原型模式

## 介绍

用原型实例指定创建对象的种类，并且通过拷贝这些原型创建新的对象

- 在抽象工厂模式中，抽象出了创建方法，使用者只能按照预定好的步骤新创建一个对象。
- 在建造者模式中，使用者可以按照自己的想法，在合理的范围内定制自己所需要的对象。
<!--more-->

## 应用场景：

-  1、当一个系统应该独立于它的产品创建，构成和表示时。 2、当要实例化的类是在运行时刻指定时，例如，通过动态装载。 3、为了避免创建一个与产品类层次平行的工厂类层次时。 4、当一个类的实例只能有几个不同状态组合中的一种时。建立相应数目的原型并克隆它们可能比每次用合适的状态手工实例化该类更方便一些。
- 细胞分裂。在这个过程中，细胞核分裂产生两个新的细胞核，其中每个都有与原来细胞完全相同的染色体和DNA

## 代码实现

### 原型模式

有时，我们需要原原本本地为对象创建一个副本。举例来说，假设你想创建一个应用来存储、分享、编辑(比如，修改、添加注释及删除)食谱。用户Bob找到一份蛋糕食谱，在做了一些改变后，觉得自己做的蛋糕非常美味，想要与朋友Alice分享这个食谱。但是该如何分享食谱呢? 如果在与Alice分享之后，Bob想对食谱做进一步的试验，Alice手里的食谱也能跟着变化吗? Bob能够持有蛋糕食谱的两个副本吗? 对蛋糕食谱进行的试验性变更不应该对原本美味蛋糕的食谱造成影响。

这样的问题可以通过让用户对同一份食谱持有多个独立的副本来解决。每个副本被称为一个克隆，是某个时间点原有对象的一个完全副本。这里时间是一个重要因素。因为它会影响克隆所包含的内容。例如，如果Bob在对蛋糕食谱做改进以臻完美之前就与Alice分享了，那么Alice就绝不可能像Bob那样烘烤出自己的美味蛋糕，只能按照Bob原来找到的食谱烘烤蛋糕。

注意引用与副本之间的区别。如果Bob和Alice持有的是同一个蛋糕食谱对象的两个引用，那么Bob对食谱做的任何改变，对于Alice的食谱版本都是可见的，反之亦然。我们想要的是Bob和Alice各自持有自己的副本，这样他们可以各自做变更而不会影响对方的食谱。实际上Bob需要蛋糕食谱的两个副本: 美味版本和试验版本。


```python
import copy

class Prototype:

    value = 'default'

    def clone(self， **attrs):
        """Clone a prototype and update inner attributes dictionary"""
        obj = copy.deepcopy(self)
        obj.__dict__.update(attrs)
        return obj


class PrototypeDispatcher:

    def __init__(self):
        self._objects = {}

    def get_objects(self):
        """Get all objects"""
        return self._objects

    def register_object(self， name， obj):
        """Register an object"""
        self._objects[name] = obj

    def unregister_object(self， name):
        """Unregister an object"""
        del self._objects[name]


def main():
    dispatcher = PrototypeDispatcher()
    prototype = Prototype()

    d = prototype.clone()
    a = prototype.clone(value='a-value'， category='a')
    b = prototype.clone(value='b-value'， is_checked=True)
    dispatcher.register_object('objecta'， a)
    dispatcher.register_object('objectb'， b)
    dispatcher.register_object('default'， d)
    print([{n: p.value} for n， p in dispatcher.get_objects().items()])

if __name__ == '__main__':
    main()

### OUTPUT ###
# [{'objectb': 'b-value'}， {'default': 'default'}， {'objecta': 'a-value'}]
```


