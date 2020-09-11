---
title: "Django中内置的signal用法"
date: 2019-07-05T10:03:48+08:00
toc: true
tags:
- python
categories:
  - 技术文章
---

Django中提供了"信号调度",用于在框架执行操作时解耦.

一些动作发生的时候,系统会根据信号定义的函数执行相应的操作
<!--more-->

# Django中内置的signal

## Model_signals

```python
pre_init                        # Django中的model对象执行其构造方法前,自动触发
post_init                       # Django中的model对象执行其构造方法后,自动触发
pre_save                        # Django中的model对象保存前,自动触发
post_save                       # Django中的model对象保存后,自动触发
pre_delete                      # Django中的model对象删除前,自动触发
post_delete                     # Django中的model对象删除后,自动触发
m2m_changed                     # Django中的model对象使用m2m字段操作数据库的第三张表(add,remove,clear,update),自动触发
class_prepared                  # 程序启动时,检测到已注册的model类,对于每一个类,自动触发
```

## migrate_signals
```python
pre_migrate                     # 执行migrate命令前,自动触发
post_migrate                    # 执行migrate命令后,自动触发 
```

## Request/response_signals

```python
request_started                 # 请求到来前,自动触发
request_finished                # 请求结束后,自动触发
got_request_exception           # 请求异常时,自动触发
```

## Test_signals

```python
setting_changed                 # 配置文件改变时,自动触发
template_rendered               # 模板执行渲染操作时,自动触发
```

## Datebase_Wrapperd

```python
connection_created              # 创建数据库连接时,自动触发
```

# 对于Django内置的信号,仅需注册指定信号,当程序执行相应操作时,系统会自动触发注册函数

在相应的应用(app)目录下的`__init__.py`文件中进行定义,(可以另外创建一个`.py`文件, 再在`__init__.py`文件导入该文件)

```python
# 导包
from django.db.models.signals import post_save
from django.dispatch import receiver
# 导入模型
from .models import MyModel
# django.db.models.signals.pre_save 在某个Model保存之前调用
# django.db.models.signals.post_save 在某个Model保存之后调用
# django.db.models.signals.pre_delete 在某个Model删除之前调用
# django.db.models.signals.post_delete 在某个Model删除之后调用
# django.core.signals.request_started 在建立Http请求时发送
# django.core.signals.request_finished 在关闭Http请求时发送
```

创建函数,监听信号, 当信号触发时,进行函数的调用

```python
# 将函数进行注册,声明为回调函数, 第一个参数为信号类型, 如果声明sender , 那么接收器只会接收这个sender的信号, 这里声明为只接收MyModel模型的信号
# post_save 在某个Model保存之后调用, 对于每个唯一的dispatch_uid,接收器都只被信号调用一次
@receiver(post_save, sender=MyModel, dispatch_uid="my_unique_identifier")
def my_handler(sender, instance, **kwargs): #参数:**kwargs必须.第一个参数必须为sender, 当信号类型为 Model_signals, 接收到的第二个参数为模型对象.　　print(instance.name) # 可以直接使用这个模型实例对象进行操作
　　print("hello world")
```

# 默认的signals及其参数

## 模型的（django/db/models/signal.py）
```python
from django.dispatch import Signal
 
class_prepared = Signal(providing_args=["class"])
 
pre_init = Signal(providing_args=["instance", "args", "kwargs"], use_caching=True)
post_init = Signal(providing_args=["instance"], use_caching=True)
 
pre_save = Signal(providing_args=["instance", "raw", "using", "update_fields"],
                 use_caching=True)
post_save = Signal(providing_args=["instance", "raw", "created", "using", "update_fields"], use_caching=True)
 
pre_delete = Signal(providing_args=["instance", "using"], use_caching=True)
post_delete = Signal(providing_args=["instance", "using"], use_caching=True)
 
pre_syncdb = Signal(providing_args=["app", "create_models", "verbosity", "interactive", "db"])
post_syncdb = Signal(providing_args=["class", "app", "created_models", "verbosity", "interactive", "db"])
 
m2m_changed = Signal(providing_args=["action", "instance", "reverse", "model", "pk_set", "using"], use_caching=True)

```

## 用户登录的(django/contrib/auth/signals.py)

```python
from django.dispatch import Signal
 
user_logged_in = Signal(providing_args=['request', 'user'])
user_login_failed = Signal(providing_args=['credentials'])
user_logged_out = Signal(providing_args=['request', 'user'])
```

## 关于request请求的(django/core/signals.py)

```python
from django.dispatch import Signal
 
request_started = Signal()
request_finished = Signal()
got_request_exception = Signal(providing_args=["request"])
```

## 数据库连接的(django/db/backends/signals.py)

```python
from django.dispatch import Signal
 
connection_created = Signal(providing_args=["connection"])
```
