---
title: "单例模式python实现"
tags:
  - python
  - 设计模式python实现
categories:
  - 设计模式
date: 2019-07-05T10:03:48+08:00
toc: true
---
# 单例模式

## 介绍

保证一个类仅有一个实例，并提供一个访问它的全局访问点。
<!--more-->

## 应用场景：

一个全局使用的类频繁地创建与销毁。


## 代码实现

### 使用函数装饰器实现单例
```python
def singleton(cls):
    _instance = {} # 使用不可变的类地址作为键，其实例作为值，每次创造实例时，首先查看该类是否存在实例，存在的话直接返回该实例即可，否则新建一个实例并存放在字典中。
    def inner():
        if cls not in _instance:
            _instance[cls] = cls()
        return _instance[cls]
    return inner
    
@singleton
class Cls(object):
    def __init__(self):
        pass

cls1 = Cls()
cls2 = Cls()
print(id(cls1) == id(cls2))
```

### 使用类装饰器实现单例
```python
class Singleton(object):
    def __init__(self, cls):
        self._cls = cls
        self._instance = {}
    def __call__(self):
        if self._cls not in self._instance:
            self._instance[self._cls] = self._cls()
        return self._instance[self._cls]

# 法一
@Singleton
class Cls2(object):
    def __init__(self):
        pass

cls1 = Cls2()
cls2 = Cls2()
print(id(cls1) == id(cls2))

# 法二
class Cls3():
    pass

Cls3 = Singleton(Cls3)
cls3 = Cls3()
cls4 = Cls3()
print(id(cls3) == id(cls4))

```

### 使用 `__new__` 创建单例
```python
class Single(object):
    _instance = None
    def __new__(cls, *args, **kw):
        if cls._instance is None:
            cls._instance = object.__new__(cls, *args, **kw)
        return cls._instance
    def __init__(self):
        pass

single1 = Single()
single2 = Single()
print(id(single1) == id(single2))
```

### 使用 元类`metaclass` 创建单例
```python
class Singleton(type):
    _instances = {}
    def __call__(cls, *args, **kwargs):
        if cls not in cls._instances:
            cls._instances[cls] = super(Singleton, cls).__call__(*args, **kwargs)
        return cls._instances[cls]

class Cls4(metaclass=Singleton):
    pass

cls1 = Cls4()
cls2 = Cls4()
print(id(cls1) == id(cls2))
```
