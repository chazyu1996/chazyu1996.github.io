---
title: "命令模式python实现"
tags:
  - python
  - 设计模式python实现
categories:
  - 设计模式
toc: true
date: 2019-11-25 22:41:46
---

# 命令模式

## 介绍

一种数据驱动的设计模式，它属于行为型模式。请求以命令的形式包裹在对象中，并传给调用对象。调用对象寻找可以处理该命令的合适的对象，并把该命令传给相应的对象，该对象执行命令。

“行为请求者”与“行为实现者”通常呈现一种“紧耦合”。但在某些场合，比如要对行为进行“记录、撤销/重做、事务”等处理，这种无法抵御变化的紧耦合是不合适的。在这种情况下，如何将“行为请求者”与“行为实现者”解耦？将一组行为抽象为对象，实现二者之间的松耦合。
<!--more-->

## 应用场景：

-  模拟 CMD。

## 代码实现

### 命令模式


```python
class Command:
    """声明命令模式接口"""
    def __init__(self, obj):
        self.obj = obj
    def execute(self):
        pass

class ConcreteCommand(Command):
    """实现命令模式接口"""
    def execute(self):
        self.obj.run()

class Invoker:
    """接受命令并执行命令的接口"""
    def __init__(self):
        self._commands = []
    def add_command(self, cmd):
        self._commands.append(cmd)
    def remove_command(self, cmd):
        self._commands.remove(cmd)
    def run_command(self):
        for cmd in self._commands:
            cmd.execute()

class Receiver:
    """具体动作"""
    def __init__(self, word):
        self.word = word
    def run(self):
        print(self.word)

def client():
    """装配者"""
    test = Invoker()
    cmd1 = ConcreteCommand(Receiver('命令一'))
    test.add_command(cmd1)
    cmd2 = ConcreteCommand(Receiver('命令二'))
    test.add_command(cmd2)
    cmd3 = ConcreteCommand(Receiver('命令三'))
    test.add_command(cmd3)
    test.run_command()

if __name__ == '__main__':
    client()
"""
--------------------------
命令一
命令二
命令三
"""
```

