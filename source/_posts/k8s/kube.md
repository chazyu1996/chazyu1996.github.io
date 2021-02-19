---
title: kubernetes 核心点
date: 2021-01-08 22:09:54
tags:
  - kubernetes
toc: true
categories:
  - 技术文章
---



kubernetes 中常见的问题总结

<!--more-->


# Log
默认情况下，如果容器重启，kubelet 会保留被终止的容器日志。
如果 Pod 在工作节点被驱逐，该 Pod 中所有的容器也会被驱逐，包括容器日志。

节点级日志记录中，需要重点考虑实现日志的轮转，以此来保证日志不会消耗节点上全部可用空间。 Kubernetes 并不负责轮转日志，而是通过部署工具建立一个解决问题的方案。

# StatefulSet

## 更新（反向更新，从大到小）

StatefulSet 里的 Pod 采用和序号相反的顺序更新。在更新下一个 Pod 前，StatefulSet 控制器终止每个 Pod 并等待它们变成 Running 和 Ready。请注意，虽然在顺序后继者变成 Running 和 Ready 之前 StatefulSet 控制器不会更新下一个 Pod，但它仍然会重建任何在更新过程中发生故障的 Pod，使用的是它们当前的版本。已经接收到更新请求的 Pod 将会被恢复为更新的版本，没有收到请求的 Pod 则会被恢复为之前的版本。像这样，控制器尝试继续使应用保持健康并在出现间歇性故障时保持更新的一致性。

### 分段更新

使用 `RollingUpdate` 更新策略的 `partition` 参数来分段更新一个 StatefulSet。分段的更新将会使 StatefulSet 中的其余所有 Pod 保持当前版本的同时仅允许改变 StatefulSet 的 `.spec.template`

### 灰度发布

## 删除(序号从小到大依次删除，创建删除都是如此顺序)

### 非级联删除

使用 kubectl delete 删除 StatefulSet。请确保提供了`--cascade=false` 参数给命令。这个参数告诉 Kubernetes 只删除 StatefulSet 而不要删除它的任何 Pod。

`kubectl delete statefulset web --cascade=false`

<font color=red>**pod 依旧存在**</font>

## 并发管理

将`.spec.podManagementPolicy` 设置成了 `Parallel`

> 创建、删除操作都会并发执行

> 更新依旧逆序执行