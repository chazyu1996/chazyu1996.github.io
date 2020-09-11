---
title: python的可变参数
date: 2019-07-05T10:03:48+08:00
toc: true
tags:
  - python
categories:
  - 技术文章
---

# python 的可变参数

在python中，通过`*`和`**`接收可变参数，分别表示 `tuple` 和`dict`，获取到值如果想以原参数传回 指定函数，也需要加上`*`和`**`
<!--more-->

```python
def test(*args,**kwargs):
    print(args)
    print(type(args))
    print(kwargs)
    print(type(kwargs))


a=(7,7,7)
b={"q":1}
test(a,b)
#((7, 7, 7), {'q': 1})
#<type 'tuple'>
#{}
#<type 'dict'>

test(*a,**b)
#(7, 7, 7)
#<type 'tuple'>
#{'q': 1}
#<type 'dict'>

```
