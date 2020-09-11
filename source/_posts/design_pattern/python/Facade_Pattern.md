---
title: "外观模式python实现"
tags:
  - python
  - 设计模式python实现
categories:
  - 设计模式
toc: true
date: 2019-11-25 22:41:46
---

# 外观模式

## 介绍

隐藏系统的复杂性，并向客户端提供了一个客户端可以访问系统的接口。这种类型的设计模式属于结构型模式，它向现有的系统添加一个接口，来隐藏系统的复杂性。
<!--more-->

## 应用场景：

-  客户端不需要知道系统内部的复杂联系，整个系统只需提供一个"接待员"即可。
-  去医院看病，可能要去挂号、门诊、划价、取药，让患者或患者家属觉得很复杂，如果有提供接待人员，只让接待人员来处理，就很方便。

## 代码实现

### 外观模式


```python
class API1:
    def Save(self):
        print('保存数据A')
    def Del(self):
        print('删除数据A')

class API2:
    def Save(self):
        print('保存数据B')
    def Del(self):
        print('删除数据B')

class Facade:
    def __init__(self):
        self._api1 = API1()
        self._api2 = API2()
    def SaveAll(self):
        [obj.Save() for obj in [self._api1, self._api2]]
    def DelAll(self):
        [obj.Save() for obj in [self._api1, self._api2]]

if __name__ == '__main__':
    test = Facade()
    test.SaveAll()
    test.DelAll()
"""
保存数据A
保存数据B
删除数据A
删除数据B
"""
```

