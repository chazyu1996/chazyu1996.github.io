---
title: "client-go"
date: 2020-10-27 20:21:52
tags:
  - kubernetes
categories:
  - 技术文章
toc: true
mathjax: true

---

# client-go 机制

在k8s中，都是通过`client-go`与`apiserver`进行交互

<!--more-->

![client-go](/images/k8s/client-go-controller-interaction.jpeg)

## 组件

`client-go` 主要由三个部分组成

* [Reflector](https://github.com/kubernetes/client-go/blob/master/tools/cache/reflector.go) ：会watch`apiserver`，并将接收到的对象推入`DeltaFIFO`队列中的`watchHandler` 函数
* [Informer](https://github.com/kubernetes/client-go/blob/master/tools/cache/controller.go) ：通过`processLoop`函数将对象从`DeltaFIFO`队列中弹出，并存入控制器中
* [Indexer](https://github.com/kubernetes/client-go/blob/master/tools/cache/index.go)：索引器可以基于几个索引功能来维护索引。索引器使用线程安全的数据存储来存储对象及其键。在包高速缓存 中的存储类型中定义了一个名为MetaNamespaceKeyFunc的默认函数，该函数生成对象的密钥作为该对象的组合。`<namespace>/<name>`

## 编写时
`Client-Go`中的基本控制器提供了`NewIndexerInformer`函数来创建`Informer`和`Indexer`。可以[直接调用函数](https://github.com/kubernetes/client-go/blob/master/examples/workqueue/main.go#L174)或者[使用工厂函数](https://github.com/kubernetes/sample-controller/blob/master/main.go#L61)创建

# 参考
[controller-client-go](https://github.com/kubernetes/sample-controller/blob/master/docs/controller-client-go.md)