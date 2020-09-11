---
title: "组合模式python实现"
tags:
  - python
  - 设计模式python实现
categories:
  - 设计模式
toc: true
date: 2019-11-25 22:41:46
---

# 组合模式

## 介绍

是用于把一组相似的对象当作一个单一的对象。组合模式依据树形结构来组合对象，用来表示部分以及整体层次。这种类型的设计模式属于结构型模式，它创建了对象组的树形结构。

这种模式创建了一个包含自己对象组的类。该类提供了修改相同对象组的方式。
<!--more-->

## 应用场景：

-  您想表示对象的部分-整体层次结构（树形结构）
-  算术表达式包括操作数、操作符和另一个操作数，其中，另一个操作符也可以是操作数、操作符和另一个操作数
-  如描述一家公司的层次结构，那么我们用办公室来表示节点，则总经理办公司是根节点，下面分别由人事办公室、业务办公室、生产办公室、财务办公室，每个办公室下面可以还有跟小的办公室，每个办公室都有职责、人员数、人员薪资等属性；

## 代码实现

### 组合模式


```python
class ComponentBases:
    """部门抽象出来的基类"""
    def __init__(self, name):
        slef.name = name

    def add(self, obj):
        pass

    def remove(self, obj):
        pass

    def display(self, number):
        pass


class Node(ComponentBases):

    def __init__(self, name, duty):
        self.name = name
        self.duty = duty
        self.children = []

    def add(self, obj):
        self.children.append(obj)

    def remove(self, obj):
        self.children.remove(obj)

    def display(self, number=1):
        print("部门：{} 级别：{} 职责：{}".format(self.name, number, self.duty))
        n = number+1
        for obj in self.children:
            obj.display(n)


if __name__ == '__main__':
    root = Node("总经理办公室", "总负责人")
    node1 = Node("财务部门", "公司财务管理")
    root.add(node1)
    node2 = Node("业务部门", "销售产品")
    root.add(node2)
    node3 = Node("生产部门", "生产产品")
    root.add(node3)
    node4 = Node("销售事业一部门", "A产品销售")
    node2.add(node4)
    node5 = Node("销售事业二部门", "B产品销售")
    node2.add(node5)
    root.display()

"""
----------输出-----------
部门：总经理办公室 级别：1 职责：总负责人
部门：财务部门 级别：2 职责：公司财务管理
部门：业务部门 级别：2 职责：销售产品
部门：销售事业一部门 级别：3 职责：A产品销售
部门：销售事业二部门 级别：3 职责：B产品销售
部门：生产部门 级别：2 职责：生产产品
"""
```

