---
title: 在访问etcd时发生了啥
tags:
  - etcd
categories:
  - 技术文章
toc: true
date: 2020-11-05 19:22:10
---

etcd 通过 raft 协议来实现 leader 选举、配置变更以及保证数据读写的一致性。那么在客户端发送或接受数据的时候发生了啥
<!--more-->

# 写数据流程：

1. etcd 任一节点的 etcd server 模块收到 Client 写请求（如果是 follower 节点，会先通过 Raft 模块将请求转发至 leader 节点处理）。
2. etcd server 将请求封装为 Raft 请求，然后提交给 Raft 模块处理。
3. leader 通过 Raft 协议与集群中 follower 节点进行交互，将消息复制到follower 节点，于此同时，并行将日志持久化到 WAL。
4. follower 节点对该请求进行响应，回复自己是否同意该请求。
5. 当集群中超过半数节点（(n/2)+1 members ）同意接收这条日志数据时，表示该请求可以被Commit，Raft 模块通知 etcd server 该日志数据已经 Commit，可以进行 Apply。
6. 各个节点的 etcd server 的 applierV3 模块异步进行 Apply 操作，并通过 MVCC 模块写入后端存储 BoltDB。
7. 当 client 所连接的节点数据 apply 成功后，会返回给客户端 apply 的结果。

# 读数据流程：

1. etcd 的任一节点的etcd server 模块接收到客户端发送的请求（Range请求）
2. 判断请求的类型，如果是串行化读（serializable）则直接进入 Apply 流程
3. 如果是线性一致性读（linearizable）则进入 Raft 模块
4. Raft 模块向 leader 发出 ReadIndex 请求，获取当前集群已经提交的最新数据 Index
5. 等待本地 AppliedIndex 大于或等于 ReadIndex 获取的 CommittedIndex 时，进入 Apply 流程

> Apply 流程：通过 Key 名从 KV Index 模块获取 Key 最新的 Revision，再通过 Revision 从 BoltDB 获取对应的 Key 和 Value。





