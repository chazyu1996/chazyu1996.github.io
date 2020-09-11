---
title: "状态模式python实现"
tags:
  - python
  - 设计模式python实现
categories:
  - 设计模式
date: 2019-07-05T10:03:48+08:00
toc: true
---

# 桥接模式

## 介绍

类的行为是基于它的状态改变的。这种类型的设计模式属于行为型模式。
<!--more-->

## 应用场景：

-  1、打篮球的时候运动员可以有正常状态、不正常状态和超常状态。  
-  2、自动售货机有不同的状态，并根据我们放入的钱币数量作出不同反应。根据我们的选择和放入的钱币，机器会执行以下操作。拒绝我们的选择，因为请求的货物已售罄; 拒绝我们的选择，因为放入的钱币不足; 递送货物，且不找零，因为放入的钱币恰好足够; 递送货物，并找零。

## 代码实现

### 状态模式


```python
class State(object):

    """Base state. This is to share functionality"""

    def scan(self):
        """Scan the dial to the next station"""
        self.pos += 1
        if self.pos == len(self.stations):
            self.pos = 0
        print(u"Scanning... Station is %s %s" %
              (self.stations[self.pos], self.name))


class AmState(State):

    def __init__(self, radio):
        self.radio = radio
        self.stations = ["1250", "1380", "1510"]
        self.pos = 0
        self.name = "AM"

    def toggle_amfm(self):
        print(u"Switching to FM")
        self.radio.state = self.radio.fmstate


class FmState(State):

    def __init__(self, radio):
        self.radio = radio
        self.stations = ["81.3", "89.1", "103.9"]
        self.pos = 0
        self.name = "FM"

    def toggle_amfm(self):
        print(u"Switching to AM")
        self.radio.state = self.radio.amstate


class Radio(object):

    """A radio.     It has a scan button, and an AM/FM toggle switch."""

    def __init__(self):
        """We have an AM state and an FM state"""
        self.amstate = AmState(self)
        self.fmstate = FmState(self)
        self.state = self.amstate

    def toggle_amfm(self):
        self.state.toggle_amfm()

    def scan(self):
        self.state.scan()


# Test our radio out
if __name__ == '__main__':
    radio = Radio()
    actions = [radio.scan] * 2 + [radio.toggle_amfm] + [radio.scan] * 2
    actions *= 2

    for action in actions:
        action()

### OUTPUT ###
# Scanning... Station is 1380 AM
# Scanning... Station is 1510 AM
# Switching to FM
# Scanning... Station is 89.1 FM
# Scanning... Station is 103.9 FM
# Scanning... Station is 81.3 FM
# Scanning... Station is 89.1 FM
# Switching to AM
# Scanning... Station is 1250 AM
# Scanning... Station is 1380 AM
```

