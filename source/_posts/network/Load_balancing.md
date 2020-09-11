---
title: 负载均衡的实现应用
date: 2020-04-09 22:24:42
tags:
  - network
categories:
  - 技术文章
toc: true
---

负载均衡是高可用架构的一个关键组件，主要用来提高性能和可用性，通过负载均衡将流量分发到多个服务器，同时多服务器能够消除这部分的单点故障。

<!--more-->

# lvs

## 相关概念

> DS：Director Server。指的是前端负载均衡器节点
> RS：Real Server。后端真实的工作服务器
> VIP：向外部直接面向用户请求，作为用户请求的目标的IP地址
> DIP：Director Server IP，主要用于和内部主机通讯的IP地址
> RIP：Real Server IP，后端服务器的IP地址
> CIP：Client IP，访问客户端的IP地址

## 四种模式

### DR模式

#### 原理

用户请求LVS到达director，director将请求的报文的目的MAC地址改为后端的realserver的MAC地址，目的IP为VIP(不变)，源IP为client IP地址(不变)，然后director将报文发送到realserver，realserver检测到目的地址为自己本地的VIP，如果在同一网段，将请求直接返回给用户，如果用户跟realserver不在同一个网段，则需要通过网关返回给用户。

#### 特点

> 前端路由将目标地址为VIP报文统统发给Director Server
> RS跟Director Server必须有一个网卡在同一个物理网络中
> 所有的请求报文经由Director Server，但响应报文必须不能进过Director Server
> 所有的real server机器上都有VIP地址

### NAT模式

#### 原理

用户请求LVS到达director，director将请求的报文的目的IP改为RIP，同时将报文的目标端口也改为realserver的相应端口，最后将报文发送到realserver上，realserver将数据返回给director，director再把数据发送给用户

#### 特点

> NAT模式修改的是目的ip，直接走的是switch不需要修改mac地址，所以VIP和RIP不需要在同一个网段内
> NAT的包的进出都需要经过LVS，所以LVS可能会成为一个系统的瓶颈问题

### FULLNAT模式

#### 特点

> FULLNAT模式也不需要DIP和RIP在同一网段
> FULLNAT和NAT相比的话：会保证RS的回包一定可到达LVS
> FULLNAT需要更新源IP，所以性能正常比NAT模式下降10%

### TUN

#### 原理

用户请求LVS到达director，director通过IP-TUN加密技术将请求报文的包封装到一个新的IP包里面，目的IP为VIP(不变)，然后director将报文发送到realserver，realserver基于IP-TUN解密，然后解析出来包的目的为VIP，检测网卡是否绑定了VIP，绑定了就处理这个包，如果在同一个网段，将请求直接返回给用户，否则通过网关返回给用户；如果没有绑定VIP就直接丢掉这个包

#### 特点

> TUNNEL必须在所有的realserver上绑定VIP
> realserver直接把包发给client
> 隧道模式运维起来会比较难，所以一般不用

## 对比

### 性能方面
DR模式、IP TUN模式都是在包进入的时候经过LVS，在包返回的时候直接返回给client；所以二者的性能比NAT高
但TUN模式更加复杂，所以性能不如DR
FULLNAT模式不仅更换目的IP还更换了源IP，所以性能比NAT下降10%
**性能比较：DR>TUN>NAT>FULLNAT**

## 负载均衡算法

### 轮叫调度 rr

均等地对待每一台服务器，不管服务器上的实际连接数和系统负载

### 加权轮叫 wrr

调度器可以自动问询真实服务器的负载情况，并动态调整权值

### 最少链接 lc

动态地将网络请求调度到已建立的连接数最少的服务器上
如果集群真实的服务器具有相近的系统性能，采用该算法可以较好的实现负载均衡

### 加权最少链接 wlc

调度器可以自动问询真实服务器的负载情况，并动态调整权值
带权重的谁不干活就给谁分配，机器配置好的权重高

### 基于局部性的最少连接调度算法 lblc

这个算法是请求数据包的目标 IP 地址的一种调度算法，该算法先根据请求的目标 IP 地址寻找最近的该目标 IP 地址所有使用的服务器，如果这台服务器依然可用，并且有能力处理该请求，调度器会尽量选择相同的服务器，否则会继续选择其它可行的服务器

### 复杂的基于局部性最少的连接算法 lblcr

记录的不是要给目标 IP 与一台服务器之间的连接记录，它会维护一个目标 IP 到一组服务器之间的映射关系，防止单点服务器负载过高。

### 目标地址散列调度算法 dh

该算法是根据目标 IP 地址通过散列函数将目标 IP 与服务器建立映射关系，出现服务器不可用或负载过高的情况下，发往该目标 IP 的请求会固定发给该服务器。

### 源地址散列调度算法 sh

与目标地址散列调度算法类似，但它是根据源地址散列算法进行静态分配固定的服务器资源。

### 最少期望延迟 sed

不考虑非活动链接，谁的权重大，优先选择权重大的服务器来接收请求，但权重大的机器会比较忙

### 永不排队 nq

无需队列，如果有realserver的连接数为0就直接分配过去

# lacp

# 等价路由

# F5

# haproxy

# oos

# iptable