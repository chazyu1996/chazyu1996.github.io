---
title: "桥接模式python实现"
tags:
  - python
  - 设计模式python实现
categories:
  - 设计模式
toc: true
date: 2019-11-25 22:41:46
---

# 桥接模式

## 介绍

用于把抽象化与实现化解耦，使得二者可以独立变化。这种类型的设计模式属于结构型模式，它通过提供抽象化和实现化之间的桥接结构，来实现二者的解耦。
<!--more-->

## 应用场景：

-   1、如果一个系统需要在构件的抽象化角色和具体化角色之间增加更多的灵活性，避免在两个层次之间建立静态的继承联系，通过桥接模式可以使它们在抽象层建立一个关联关系。 
-  2、对于那些不希望使用继承或因为多层次继承导致系统类的个数急剧增加的系统，桥接模式尤为适用。
-   3、一个类存在两个独立变化的维度，且这两个维度都需要进行扩展。

## 代码实现

### 桥接模式


```python
class A:

    def run(self, name):
        print("my name is :{}".format(name))


class B:

    def run(self, name):
        print("我的名字是：{}".format(name))


class Bridge:

    def __init__(self, ager, classname):
        self.ager = ager
        self.classname = classname

    def bridge_run(self):
        self.classname.run(self.ager)


if __name__ == '__main__':
    test = Bridge('李华', A())
    test.bridge_run()
    test.ager = 'Tome'
    test.bridge_run()
    test.classname = B()
    test.bridge_run()
    test.ager = '李华'
    test.bridge_run()
```

