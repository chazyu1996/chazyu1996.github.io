---
title: "模板模式python实现"
tags:
  - python
  - 设计模式python实现
categories:
  - 设计模式
date: 2019-07-05T10:03:48+08:00
toc: true
---

# 模板模式

## 介绍

一个抽象类公开定义了执行它的方法的方式/模板。它的子类可以按需要重写方法实现，但调用将以抽象类中定义的方式进行。这种类型的设计模式属于行为型模式。
<!--more-->

## 应用场景：

-   1、有多个子类共有的方法，且逻辑相同。 
-   2、重要的、复杂的方法，可以考虑作为模板方法。

## 代码实现

### 模板模式


```python
# 茶的制作方法
class Tea:

    def prepare_recipe(self):
        # 在下边实现具体步骤
        self.boil_water()
        self.brew_tea_bag()
        self.pour_in_cup()
        
    def boil_water(self):
        print("Boiling water")
        
    def brew_tea_bag(self):
        print("Steeping the tea")
        
    def pour_in_cup(self):
        print("Pouring into cup")
# 咖啡的制作方法
class Coffee:

    def prepare_recipe(self):
        # 在下边实现具体步骤
        self.boil_water()
        self.brew_coffee_grinds()
        self.pour_in_cup()
        self.add_sugar_and_milk()
        
    def boil_water(self):
        print("Boiling water")
        
    def brew_coffee_grinds(self):
        print("Dripping Coffee through filter")
        
    def pour_in_cup(self):
        print("Pouring into cup")
        
    def add_sugar_and_milk(self):
        print("Adding Sugar and Milk")

```

模板模式改后

```python
class CoffeineBeverage:

    def prepare_recipe(self):
        # 新的实现方法
        self.boil_water()
        self.brew() 
        self.pour_in_cup()
        self.add_condiments()
        
    def boil_water(self):
        print("Boiling water")
        
    def brew(self):
        # 需要在子类实现
        raise NotImplementedError
        
    def pour_in_cup(self):
        print("Pouring into cup")
        
    def add_condiments(self):
        # 这里其实是个钩子方法，子类可以视情况选择是否覆盖
        # 钩子方法是一个可选方法，也可以让钩子方法作为某些条件触发后的动作
        pass

# 茶的制作方法
class Tea(CoffeineBeverage):
        
    def brew(self):
        # 父类中声明了 raise NotImplementedError，这里必须要实现此方法
        print("Steeping the tea")
        
    # Tea 不需要 add_condiments 方法，所以这里不需要实现

# 咖啡的制作方法
class Coffee(CoffeineBeverage):
        
    def brew(self):
        # 父类中声明了 raise NotImplementedError，这里必须要实现此方法
        print("Dripping Coffee through filter")
        
    def add_condiments(self):
        print("Adding Sugar and Milk")

```

