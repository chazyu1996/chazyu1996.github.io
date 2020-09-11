---
title: "解释器模式python实现"
tags:
  - python
  - 设计模式python实现
categories:
  - 设计模式
toc: true
date: 2019-11-25 22:41:46
---

# 解释器模式

## 介绍

开发者自定义一种“有内涵”的语言（或者叫字符串），并设定相关的解释规则，输入该字符串后可以输出公认的解释，或者执行程序可以理解的动作。这种模式被用在 SQL 解析、符号处理引擎等
<!--more-->

## 应用场景：

-  如果一种特定类型的问题发生的频率足够高，那么可能就值得将该问题的各个实例表述为一个简单语言中的句子。这样就可以构建一个解释器，该解释器通过解释这些句子来解决该问题。

## 代码实现

### 解释器模式

```python
import time
import datetime
"""实现一段简单的中文编程"""

class Code:
    """自定义语言"""
    def __init__(self, text=None):
        self.text = text

class InterpreterBase:
    """自定义解释器基类"""
    def run(self, code):
        pass

class Interpreter(InterpreterBase):
    """实现解释器方法，实现终结符表达式字典"""
    def run(self, code):
        code = code.text
        code_dict = {'获取当前时间戳': time.time(), "获取当前日期": datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")}
        print(code_dict.get(code))

if __name__ == '__main__':
    test = Code()
    test.text = '获取当前时间戳'
    data1 = Interpreter().run(test)
    test.text = '获取当前日期'
    data2 = Interpreter().run(test)
"""
-----------------
1550156061.1181707
2019-02-14 22:54:21
"""
```
## 解释器模式要实现两个核心角色：

- 终结符表达式：实现与文法中的元素相关联的解释操作，通常一个解释器模式中只有一个终结符表达式，但有多个实例，对应不同的终结符。终结符一半是文法中的运算单元，比如有一个简单的公式R=R1+R2，在里面R1和R2就是终结符，对应的解析R1和R2的解释器就是终结符表达式。

- 非终结符表达式：文法中的每条规则对应于一个非终结符表达式，非终结符表达式一般是文法中的运算符或者其他关键字，比如公式R=R1+R2中，+就是非终结符，解析+的解释器就是一个非终结符表达式。非终结符表达式根据逻辑的复杂程度而增加，原则上每个文法规则都对应一个非终结符表达式。
