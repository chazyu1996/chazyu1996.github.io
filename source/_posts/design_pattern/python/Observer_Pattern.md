---
title: "观察者模式python实现"
tags:
  - python
  - 设计模式python实现
categories:
  - 设计模式
date: 2019-07-05T10:03:48+08:00
toc: true
---

# 观察者模式

## 介绍

定义对象间的一种一对多的依赖关系 ,当一个对象的状态发生改变时 , 所有依赖于它的对象都得到通知并被自动更新。
<!--more-->

## 应用场景：

-   当一个抽象模型有两个方面 , 其中一个方面依赖于另一方面。将这二者封装在独立的对象中以使它们可以各自独立地改变和复用。
-   当对一个对象的改变需要同时改变其它对象 , 而不知道具体有多少对象有待改变。
-   当一个对象必须通知其它对象，而它又不能假定其它对象是谁。换言之 , 你不希望这些对象是紧密耦合的。

## 代码实现

### 观察者模式


```python
class AbstractObservable(object):
    def register(self):
        raise NotImplementedError(
            'register is a abstract method which must be implemente')

    def remove(self):
        raise NotImplementedError(
            'remove is a abstract method which must be implemente')

class AbstractDisplay(object):
    def update(self):
        raise NotImplementedError(
            'update is a abstract method which must be implemente')

    def display(self):
        raise NotImplementedError(
            'display is a abstract method which must be implemente')
class Subject(object):
    def __init__(self, subject):
        self.subject = subject
        self._observers = []

    def register(self, ob):
        self._observers.append(ob)

    def remove(self, ob):
        self._observers.remove(ob)

    def notify(self, data=None):
        for ob in self._observers:
            ob.update(data)

class WeatherData(AbstractObservable):
    def __init__(self, *namespaces):
        self._nss = {}
        self._clock = None
        self._temperature = None
        self._humidity = None
        self._oxygen = None

        for ns in namespaces:
            self._nss[ns] = Subject(ns)

    def register(self, ns, ob):
        if ns not in self._nss:
            raise Exception('this {} is invalid namespace'.format(ns))
        self._nss[ns].register(ob)

    def remove(self, ns, ob):
        return self._nss[ns].remove(ob)

    def set_measurement(self, data):
        # 此处实现可以更加紧凑，但是为了表达更简单，采用如下方式
        self._clock = data['clock']
        self._temperature = data['temperature']
        self._humidity = data['humidity']
        self._oxygen = data['oxygen']

        for k in self._nss.keys():
            if k != 'all':
                data = self

            self._nss[k].notify(data)

    # 以下 property 为了实现 pull 模式

    @property
    def clock(self):
        return self._clock

    @property
    def temperature(self):
        return self._temperature

    @property
    def humidity(self):
        return self._humidity
    @property
    def oxygen(self):
        return self._oxygen
```

观察者模式的可观察对象实现可以分成两种实现方案：

- push 模式
- pull 模式

push 模式能保证所有的观察者可以接收到全部的数据，无论需要不需要，频繁更新会影响性能。

pull 模式需要观察者自己拉去数据，实现起来比较容易出错，但是能按需获取信息。

```python
class OverviewDisplay(AbstractDisplay):
    def __init__(self):
        self._data = {}

    def update(self, data):
        self._data = data
        self.display()

    def display(self):
        print(u'总览显示面板：')
        for k, v in self._data.items():
            print(k + ': ' + str(v))
class TemperatureDisplay(AbstractDisplay):
    def __init__(self):
        self._storage = []

    def update(self, data):
        dt = data.clock
        temperature = data.temperature
        self._storage.append((dt, temperature))
        self.display()

    def display(self):
        print(u'温度显示面板：')
        for storey in self._storage:
            print(storey[0] + ': ' + str(storey[1]))

if __name__ == '__main__':
    import time

    # 生成一个可观察对象，支持('all', 'temperature', 'humidity', 'oxygen')的数据通知
    wd = WeatherData('all', 'temperature', 'humidity', 'oxygen')

    # 两个观察者对象
    od = OverviewDisplay()
    td = TemperatureDisplay()

    # 注册到可观察对象中，能获取数据更新
    wd.register('all', od)
    wd.register('temperature', td)

    # 更新数据，可观察对象将会自动更新数据
    wd.set_measurement({
        'clock': time.strftime("%Y-%m-%d %X", time.localtime()),
        'temperature': 20,
        'humidity': 60,
        'oxygen': 10
    })

    # 一秒后再次更新数据
    time.sleep(1)
    print('\n')
    wd.set_measurement({
        'clock': time.strftime("%Y-%m-%d %X", time.localtime()),
        'temperature': 21,
        'humidity': 58,
        'oxygen': 7
    })

"""
总览显示面板：
humidity: 60
temperature: 20
oxygen: 10
clock: 2017-03-26 18:08:41
温度显示面板：
2017-03-26 18:08:41: 20

总览显示面板：
humidity: 58
temperature: 21
oxygen: 7
clock: 2017-03-26 18:08:42
温度显示面板：
2017-03-26 18:08:41: 20
2017-03-26 18:08:42: 21
"""
```

