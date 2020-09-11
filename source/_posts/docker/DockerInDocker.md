---
title: "DockerInDocker"
date: 2019-07-22T14:59:11+08:00
tags:
  - docker
categories:
  - 技术文章
toc: true

---

Docker技术目前在DevOps中被广泛使用，我们需要将测试或者构建的代码和自动化脚本打包成Docker镜像，然后部署在各运行环境中。而在CI/CD中，我们常用一些CI/CD服务器，比如Jenkins和GoCD来构建与部署我们的应用，从而实现CI/CD的自动化。现在一些CI/CD服务器也被Docker化运行在真实的物理机上。于是我们需要在CI/CD服务器的Docker container里面来构建（build）与运行（run）我们的Docker镜像，这就涉及到"Docker run Docker"的问题。
<!--more-->

一个很自然的想法是，我们是不是需要在CI/CD服务器镜像中安装一个Docker Daemon和Docker命令呢？但是Docker里面跑Docker总感觉有些蹩脚，额外安装与运行Docker无疑增加了CI/CD服务器镜像的大小，同时还增加Docker的深度。

实际上，我们并不需要在CI/CD服务器上安装Docker。通过如下的命令在CI/CD服务器上运行我们的镜像：

```shell
docker run  -v /var/run/docker.sock:/var/run/docker.sock
```

# 原理

&emsp;&emsp;Docker采取的是C/S架构，Docker的成功运行需要Docker Daemon和Docker Client(客户端)的支持，当我们运行一些docker build等命令时，实际是需要Docker Client连接Docker Daemon发送命令，Docker Daemon会在宿主机操作系统分配文件、网络等资源。

![DockerInDocker](/images/docker/DockerInDocker.jpg)

&emsp;&emsp;默认情况下，Docker守护进程会生成一个socket（/var/run/docker.sock）文件来进行本地进程通信，而不会监听任何端口，因此只能在本地使用docker客户端或者使用Docker API进行操作。一般情况下，我们访问本机的服务往往通过 127.0.0.1:8080 这种IP：端口的网络地址方式进行通信，而sock文件是 UNIX 域套接字（UNIX domain socket），它可以通过文件系统（而非网络地址）进行寻址和访问的套接字。

&emsp;&emsp;从表象上看，上面的命令似乎依然是在“Docker里面run docker”，其实这是个误区。docker run提供了 -v  参数让我们将宿主的文件映射到docker里面。比如通过 -v /var/run/docker.sock:/var/run/docker.sock ，我们将宿主的Docker Daemon的socket映射到Docker Container里面；当Container里面的docker 客户端通过 /var/run/docker.sock 去操作Docker Daemon时，这些操作已移花接木地转移到宿主的Docker Daemon上。


# 延伸：操作远程主机

&emsp;&emsp;既然docker client通过socket方式与本地的Docker Daemon进行通信，那么我们可以很自然地想到，如果想在其他主机上通过socket连接到远程DockerDaemon，是不是可以远程操作Docker主机进行镜像的构建与运行呢？答案是可以的，就需要让Docker守护进程监听一个端口，这样才能实现远程通信，同时需要修改docker客户端连接的主机是远程地址而并非本地sock文件。由于Docker C/S 之间采取Rest API作为通信协议，这为我们使用第三方客户端（如postman）操作docker乃至自己开发client提供了扩展。
