---
title: kube-scheduler 的扩展
date: 2020-11-08 22:09:54
tags:
  - kubernetes
toc: true
categories:
  - 技术文章
---

k8s官方默认的scheduler包含了一些调度算法，但是有些时候不能很好的满足需求。
比如：一台物理机多个虚拟机，每个虚拟机是一个`node`节点，相同的服务调度时，希望尽量分散在不同的物理机上。
这时候，我们需要将scheduler进行扩展，通过自定义算法，进一步控制调度逻辑、节点score。

<!--more-->

## 扩展方案

目前Kubernetes支持四种方式实现客户自定义的调度算法(预选&优选)，如下：

1. default-scheduler recoding:直接在Kubernetes默认scheduler基础上进行添加，然后重新编译kube-scheduler

2. standalone:实现一个与kube-scheduler平行的custom scheduler，单独或者和默认kube-scheduler一起运行在集群中

3. scheduler extender:实现一个"scheduler extender"，kube-scheduler会调用它(http/https)作为默认调度算法(预选&优选&bind)的补充

4. scheduler framework:实现scheduler framework plugins，重新编译kube-scheduler，类似于第一种方案，但是更加标准化，插件化

