---
title: pod 的创建过程
date: 2020-09-08T22:09:54+08:00
tags:
  - kubernetes
toc: true
categories:
  - 技术文章
---

# pod的创建过程

kubernetes 中各个组件都是以 `watch` 的方式从`kube-apiserver`中获取信息。当然，这其中也用到了`informer`的缓存机制

<!--more-->

![pod的创建](/images/k8s/create_pod.png)


1. 用户通过Kubectl提交Pod Spec给API Server；
2. API Server将Pod对象的信息存入Etcd中
3. Pod的创建会生成一个事件，返回给API Server
4. Controller监听到这个事件
5. Controller知道这个Pod要mount一个盘，于是查看是否有能够满足条件的PV
6. 假设有满足条件的PV，就将Pod和PV绑定在一起，将绑定关系告知API Server
7. API Server将绑定信息写入Etcd中
8. 生成一个Pod Update事件
9. Scheduler监听到了这个事件
10. Scheduler需要为Pod选择一个Node
11. 假设有满足条件的Node，就讲Pod和Node绑定在一起，将绑定关系告知API Server
12. API Server将绑定信息写入Etcd中
13. 生成一个Pod Update事件
14. Kubelet监听到了这个事件，开始创建Pod
15. Kubelet告知CRI去下载镜像
16. Kubelet告知CRI去运行容器
17. CRI调用Docker运行容器
18. Kubelet告知Volume Manager，将盘挂在到Node上，然后mount到Pod中
19. CRI调用CNI给容器配置网络


![pod的创建](/images/k8s/kubelet_create_pod.png)
