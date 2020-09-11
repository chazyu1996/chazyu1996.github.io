---
title: "备忘录模式python实现"
tags:
  - python
  - 设计模式python实现
categories:
  - 设计模式
date: 2019-07-05T10:03:48+08:00
toc: true
---

# 备忘录模式

## 介绍

在不破坏封闭的前提下，捕获一个对象的内部状态，并在该对象之外保存这个状态。这样以后就可将该对象恢复到原先保存的状态。简单来说在运行过程中我们可以记录某个状态，当遇到错误时恢复当前状态，这在业务流程中是用设计来处理异常情况。
<!--more-->

## 应用场景：

-   有时一些发起人对象的内部信息必须保存在发起人对象以外的地方，但是必须要由发起人对象自己读取，这时，使用备忘录模式可以把复杂的发起人内部信息对其他的对象屏蔽起来，从而可以恰当地保持封装的边界。
-    需要保存/恢复数据的相关状态场景。
-   提供一个可回滚的操作。

## 代码实现

### 备忘录模式


```python
class AddNumber:
    def __init__(self):
        self.start = 1
    def add(self, number):
        self.start += number
        print(self.start)

class Memento:
    """备忘录"""
    def backups(self, obj=None):
        """
        设置备份方法
        :param obj: 
        :return: 
        """
        self.obj_dict = copy.deepcopy(obj.__dict__)
        print("备份数据:{}".format(self.obj_dict))
    def recovery(self, obj):
        """
        恢复备份方法
        :param obj: 
        :return: 
        """
        obj.__dict__.clear()
        obj.__dict__.update(self.obj_dict)
        return obj

if __name__ == '__main__':
    test = AddNumber()
    memento = Memento()
    for i in [1, 2, 3, 'n', 4]:
        if i == 2:
            memento.backups(test)
        try:
            test.add(i)
        except TypeError as e:
            print(e)
            print(test.start)
    memento.recovery(test)
    print(test.start)

"""
--------------------
2
备份数据:{'start': 2}
4
7
unsupported operand type(s) for +=: 'int' and 'str'
7
11
2
"""
```

