---
title: "云原生"
date: 2020-07-29 20:16:11
toc: true
tags:
- CNCF
categories:
  - 技术文章
  - 概念描述
---



# 云原生

云原生，作为这个时期最热门的名词，在微服务的驱动下，更加加速了他的发展。
<!--more-->

# 云原生的设计理念

云原生系统的设计理念如下:

* 面向分布式设计（Distribution）：容器、微服务、API 驱动的开发；
* 面向配置设计（Configuration）：一个镜像，多个环境配置；
* 面向韧性设计（Resistancy）：故障容忍和自愈；
* 面向弹性设计（Elasticity）：弹性扩展和对环境变化（负载）做出响应；
* 面向交付设计（Delivery）：自动拉起，缩短交付时间；
* 面向性能设计（Performance）：响应式，并发和资源高效利用；
* 面向自动化设计（Automation）：自动化的 DevOps；
* 面向诊断性设计（Diagnosability）：集群级别的日志、metric 和追踪；
* 面向安全性设计（Security）：安全端点、API Gateway、端到端加密；

# 实现云原生应用程序所需特性的常用方法

## 微服务
拆就完事了
## 健康报告
程序自身的心跳接口，只要正常，说明程序正常。

应用程序不仅仅有健康或不健康的状态。它们将经历一个启动和关闭过程，在这个过程中它们应该通过健康检查，报告它们的状态。如果应用程序可以让平台准确了解它所处的状态，平台将更容易知道如何操作它。

一个很好的例子就是当平台需要知道应用程序何时可以接收流量。在应用程序启动时，如果它不能正确处理流量，它就应该表现为未准备好。此额外状态将防止应用程序过早终止，因为如果运行状况检查失败，平台可能会认为应用程序不健康，并且会反复停止或重新启动它。

应用程序健康只是能够自动化应用程序生命周期的一部分。除了知道应用程序是否健康之外，您还需要知道应用程序是否正在进行哪些工作。这些信息来自遥测数据。
## 遥测数据
遥测数据是进行决策所需的信息。确实，遥测数据可能与健康报告重叠，但它们有不同的用途。健康报告通知我们应用程序生命周期状态，而遥测数据通知我们应用程序业务目标。

您测量的指标有时称为服务级指标（SLI）或关键性能指标（KPI）。这些是特定于应用程序的数据，可以确保应用程序的性能处于服务级别目标（SLO）内。

> 请求数量
> 错误率
> 时长


## 弹性

### 为失败设计
唯一永远不会失败的系统是那些让你活着的系统（例如心脏植入物和刹车系统）。如果您的服务永远不会停止运行，您需要花费太多时间设计它们来抵制故障，并且没有足够的时间增加业务价值。您的SLO确定服务需要多长时间。您花费在工程设计上超出SLO的正常运行时间的任何资源都将被浪费掉。

您应该为每项服务测量两个值，即平均无故障时间（MTBF）和平均恢复时间（MTTR）。监控和指标可以让您检测您是否符合您的SLO，但运行应用程序的平台是保持高MTBF和低MTTR的关键。

在任何复杂的系统中，都会有失败。您可以管理硬件中的某些故障（例如，RAID和冗余电源），以及某些基础设施中的故障（例如负载平衡器）。但是因为应用程序知道他们什么时候健康，所以他们也应该尽可能地管理自己的失败。

设计一个以失败期望为目标的应用程序将比假定可用性的应用程序更具防御性。当故障不可避免时，将会有额外的检查，故障模式和日志内置到应用程序中。

知道应用程序可能失败的每种方式是不可能的。假设任何事情都可能并且可能会失败，这是一种云原生应用程序的模式。

您的应用程序的最佳状态是健康状态。第二好的状态是失败状态。其他一切都是非二进制的，难以监控和排除故障。 Honeycomb首席执行官CharityMajors在她的文章“Ops：现在每个人都在工作”中指出：“分布式系统永远不会起作用；它们处于部分退化服务的持续状态。接受失败，设计弹性，保护和缩小关键路径。”

无论发生什么故障，云原生应用程序都应该是可适应的。他们期望失败，所以他们在检测到时进行调整。

有些故障不能也不应该被设计到应用程序中（例如，网络分区和可用区故障）。该平台应自主处理未集成到应用程序中的故障域。

### 优雅降级

云原生应用程序需要有一种方法来处理过载，无论它是应用程序还是负载下的相关服务。处理负载的一种方式是优雅降级。 “站点可靠性工程”一书中描述了应用程序的优雅降级，因为它提供的响应在负载过重的情况下“不如正常响应准确或含有较少数据的响应，但计算更容易”。

减少应用程序负载的某些方面由基础设施处理。智能负载平衡和动态扩展可以提供帮助，但是在某些时候，您的应用程序可能承受的负载比它可以处理的负载更多。云原生应用程序需要知道这种必然性并作出相应的反应。

优雅降级的重点是允许应用程序始终返回请求的答案。如果应用程序没有足够的本地计算资源，并且依赖服务没有及时返回信息，则这是正确的。依赖于一个或多个其他服务的服务应该可用于应答请求，即使依赖于服务不是。当服务退化时，返回部分答案或使用本地缓存中的旧信息进行答案是可能的解决方案。

## 声明式的，而不是命令式的

声明式通信模型由于多种原因而变得更加健壮。最重要的是，它规范了通信模型，并且它将功能实现从应用程序转移到远程API或服务端点，从而实现某种状态到达期望状态。这有助于简化应用程序，并使它们彼此的行为更具可预测性。