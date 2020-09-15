---
title: "kube-scheduler 源码阅读"
date: 2020-09-14 20:21:52
tags:
  - kubernetes
categories:
  - 技术文章
toc: true
mathjax: true

---

# kube-scheduler

自动调度是云原生最重要的环节之一，kube-scheduler是Kubernetes中的关键模块，扮演管家的角色遵从一套机制为Pod提供调度服务，例如基于资源的公平调度、调度Pod到指定节点、或者通信频繁的Pod调度到同一节点等。

<!--more-->

容器调度本身是一件比较复杂的事，因为要确保以下几个目标：

1. 公平性：在调度Pod时需要公平的进行决策，每个节点都有被分配资源的机会，调度器需要对不同节点的使用作出平衡决策。
2. 资源高效利用：最大化群集所有资源的利用率，使有限的CPU、内存等资源服务尽可能更多的Pod。
3. 效率问题：能快速的完成对大批量Pod的调度工作，在集群规模扩增的情况下，依然保证调度过程的性能。
4. 灵活性：在实际运作中，用户往往希望Pod的调度策略是可控的，从而处理大量复杂的实际问题。因此平台要允许多个调度器并行工作，同时支持自定义调度器。

为达到上述目标，kube-scheduler通过结合Node资源、负载情况、数据位置等各种因素进行调度判断，确保在满足场景需求的同时将Pod分配到最优节点。

## 预选和优选

kube-scheduler的根本工作任务是根据各种调度算法将Pod绑定（bind）到最合适的工作节点，整个调度流程分为两个阶段：预选策略（Predicates）和优选策略（Priorities）。

预选（Predicates）：输入是所有节点，输出是满足预选条件的节点。kube-scheduler根据预选策略过滤掉不满足策略的Nodes。例如，如果某节点的资源不足或者不满足预选策略的条件如“Node的label必须与Pod的Selector一致”时则无法通过预选。
优选（Priorities）：输入是预选阶段筛选出的节点，优选会根据优先策略为通过预选的Nodes进行打分排名，选择得分最高的Node。例如，资源越富裕、负载越小的Node可能具有越高的排名。

通俗点说，调度的过程就是在回答两个问题：1. 候选有哪些？2. 其中最适合的是哪个？

值得一提的是，如果在预选阶段没有节点满足条件，Pod会一直处在Pending状态直到出现满足的节点，在此期间调度器会不断的进行重试。

[pod 的创建过程]: https://chazyu.com/2020/09/08/k8s/what_happen_apply_yaml/	"pod 的创建过程"

### 预选策略（Predicates）

#### 基于存储卷数量的判断

> MaxEBSVolumeCount：确保已挂载的EBS存储卷数量不超过设置的最大值（默认39），调度器会检查直接或及间接使用这种类型存储的PVC，累加总数，如果卷数目超过设最大值限制，则不能调度新Pod到这个节点上。

> MaxGCEPDVolumeCount：同上，确保已挂载的GCE存储卷数量不超过预设的最大值（默认16）。

> MaxAzureDiskVolumeCount：同上，确保已挂载的Azure存储卷不超过设置的最大值（默认16）。

#### 基于资源压力状态的判断

> CheckNodeMemoryPressure：判断节点是否已经进入到内存压力状态，如果是则只允许调度内存为0标记的Pod。

> CheckNodeDiskPressure：判断节点是否已经进入到磁盘压力状态，如果是，则不能调度新的Pod。

#### 基于卷冲突的判断

> NoDiskConflict：卷冲突判断，即如果该节点已经挂载了某个卷，其它同样使用相同卷的Pod将不能再调度到该节点。

> NoVolumeZoneConflict：对于给定的某块区域，判断如果在此区域的节点上部署Pod是否存在卷冲突。

> NoVolumeNodeConflict：对于某个指定节点，检查如果在此节点上部署Pod是否存在卷冲突。

#### 基于约束关系的判断

> MatchNodeSelector：检查节点标签（label）是否匹配Pod指定的nodeSelector，是则通过预选。

> MatchInterPodAffinity：根据Pod之间的亲和性做判断。

> PodToleratesNodeTaints：排斥性关系，即判断Pod不允许被调度到哪些节点。这里涉及到两个概念Taints（污点）和Toleration（容忍）。Node可以定义一或多个Taint，Pod可以定义一或多个Toleration，对于具有某个Taint的节点，只有遇到能容忍它的（即带有对应Toleration的）Pod，才允许Pod被调度到此节点，从而避免Pod被分配到不合适的节点。

#### 基于适合性的判断

> PodFitsResources：检查节点是否有足够资源（如CPU、内存、GPU等）满足Pod的运行需求。

> PodFitsHostPorts：检查Pod容器所需的HostPort是否已被节点上其它容器或服务占用。如果已被占用，则禁止Pod调度到该节点。

> PodFitsHost：检查Pod指定的NodeName是否匹配当前节点。


### 优选策略（Priorities）

优选过程会根据优选策略对每个候选节点进行打分，最终把Pod调度到分值最高的节点。kube-scheduler用一组优先级函数处理每个通过预选的节点，每个函数返回0-10的分数，各个函数有不同权重，最终得分是所有优先级函数的加权和，即节点得分的计算公式为

$FinalNodeScore =\sum Weight_i \times PriorityFunc_i$

代码中大部分采用字典的形式进行传输，所以需要一个映射关系，`pkg/scheduler/framework/plugins/registry.go`文件中记录了默认算法的名称映射关系。

优选的优先级函数包括: 

#### {Name: noderesources.BalancedAllocationName, Weight: 1}

CPU和内存使用率越接近的节点权重越高。该策略均衡了节点CPU和内存的配比，尽量选择在部署Pod后各项资源更均衡的机器。

#### {Name: imagelocality.Name, Weight: 1}

尽量调度到Pod所需镜像的节点。检查Node是否存在Pod所需镜像：如果不存在，返回0分；如果存在，则镜像越大得分越高

#### {Name: interpodaffinity.Name, Weight: 1}

叠加该节点已调度的每个Pod的权重，权重可配置文件里设定，通常亲和性越强的Pod权重越高，如果Pod满足要求则将加到权重和，具有最高权重和的节点是最优的。

#### {Name: noderesources.LeastAllocatedName, Weight: 1}

尽量将Pod调度到计算资源占用比较小的Node上，这里涉及两种计算资源：内存和CPU

#### {Name: nodeaffinity.Name, Weight: 1}

nodeselector 按照节点选择器选择

#### {Name: nodepreferavoidpods.Name, Weight: 10000}

避免将ReplicationController或ReplicaSet调度到节点。如果Pod由RC或者RS的Controller调度，则得分为0，不对最终加权得分产生影响；如果不是，则总分为100000，意味着覆盖其他策略，直接决定最优节点。

#### {Name: podtopologyspread.Name, Weight: 2}

拓扑约束

#### {Name: tainttoleration.Name, Weight: 1}

污点容忍
