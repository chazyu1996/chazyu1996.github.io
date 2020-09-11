---
title: "建造者模式python实现"
tags:
  - python
  - 设计模式python实现
categories:
  - 设计模式
toc: true
date: 2019-11-25 22:41:46
---

# 建造者模式

## 介绍

建造者模式（生成器模式、Buidler Pattern）和抽象工厂模式的目的都是用来创建复杂的对象，但是创建的过程是截然不同的。

- 在抽象工厂模式中，抽象出了创建方法，使用者只能按照预定好的步骤新创建一个对象。
- 在建造者模式中，使用者可以按照自己的想法，在合理的范围内定制自己所需要的对象。
<!--more-->

## 应用场景：

- 对象的创建步骤可以独立于创建过程的时候
- 被创建的对象拥有不同的表现形式

## 代码实现

### 建造者模式

举一个栗子，我现在要买一台新版 15 寸的 Macbook Pro，打开官网发现有标准版和定制版两种可以选择。
所以现在有两种选择，购买标准版和购买定制版，定制版可以 SSD 升级到 1TB，可以把 CPU 升级到 2.7GHz，但是不允许升级内存到 32G，因为苹果说 32G 内存效果提升不大，还更加耗电。
这么一来我们就可在合理的范围内定制一台适合自己的笔记本。和购买标准版的结果一样，最后的结果都是可以获得一台新的 Macbook Pro。
类比到建造者模式和抽象工厂模式就是定制版和标准版。
总结一句话：建造者模式注重一步一步创建对象，抽象工厂模式注重一步到位创建对象。
建造者由 Director 和 Builder 构成，Builder 用于抽象各个对象部件的接口，Director 用于构造一个 Builder 的接口，由 Director 去指导 Builder 如何生成一个复杂对象。

以购买一台 2016 款 15 寸 Macbook Pro 为背景描述一个建造者模式。

我们在线购买时候 online shop 就是一个 Director，它一步一步指导我们去购买一个 Macbook Pro。
```python
from collections import namedtuple


class OnlineShop(object):
    def __init__(self, buidler):
        Macbook = namedtuple('Macbook', 'cpu memory ssd graphics')
        self.macbook = Macbook(buidler.cpu, buidler.memory,
                               buidler.ssd, buidler.graphics)

    def __str__(self):
        return str(self.macbook)

    class MacbookBuilder(object):
        def __init__(self):
            self.cpu = '2.7GHz'
            self.memory = '16G'
            self.ssd = '512GB'
            self.graphics = 'Radeon Pro 455'

        def upgrade_cpu(self, cpu):
            self.cpu = cpu
            return self

        def upgrade_memory(self, memory):
            raise ValueError('{0} is max'.format(self.memory))

        def upgrade_ssd(self, ssd):
            self.ssd = ssd
            return self

        def upgrade_graphics(self, graphics):
            self.graphics = graphics
            return self
```
OnlineShop 作为一个 Director 被客户端调用，MacbookBuilder 作为一个 Builder 是不能被客户端调用，只能被 Director 所调用，直接在类中定义另一个类，是防止被调用的简洁实现方式。

```
macbook = OnlineShop.MacbookBuilder().upgrade_cpu('2.9GHz').upgrade_ssd('2TB').upgrade_graphics('Radeon Pro 460').build_up()

```
