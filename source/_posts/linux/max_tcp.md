---
title: "最大TCP连接数"
date: 2019-08-26T17:01:01+08:00
tags:
   - linux
categories:
  - 技术文章
toc: true
---
# linux 的TCP最大连接

## 最大理论连接数
接受端口：65535
发送端口:65535
那么 ip A --> ipB，组合起来有 65535*65535 

<!--more-->
## 如何标识一个TCP连接

在确定最大连接数之前，先来看看系统如何标识一个tcp连接。系统用一个4四元组来唯一标识一个TCP连接：{local ip, local port,remote ip,remote port}。

## client最大tcp连接数

client每次发起tcp连接请求时，除非绑定端口，通常会让系统选取一个空闲的本地端口（local port），该端口是独占的，不能和其他tcp连接共享。tcp端口的数据类型是unsigned short（无符号短整型0~65535），因此本地端口个数最大只有65536，端口0有特殊含义，不能使用，这样可用端口最多只有65535，所以在全部作为client端的情况下，最大tcp连接数为65535，这些连接可以连到不同的server ip。

## server最大tcp连接数

server通常固定在某个本地端口上监听，等待client的连接请求。不考虑地址重用（unix的SO_REUSEADDR选项）的情况下，即使server端有多个ip，本地监听端口也是独占的。
因此server端tcp连接4元组中只有remote ip（也就是client ip）和remote port（客户端port）是可变的，因此最大tcp连接为客户端ip数×客户端port数，对IPV4，不考虑ip地址分类等因素，最大tcp连接数约为2的32次方（ip数）×2的16次方（port数），也就是server端单机最大tcp连接数约为2的48次方。

## 实际的tcp连接数

上面给出的是理论上的单机最大连接数，在实际环境中，受到机器资源、操作系统等的限制，特别是sever端，其最大并发tcp连接数远不能达到理论上限。在unix/linux下限制连接数的主要因素是内存和允许的文件描述符个数（句柄），每个tcp连接都要占用一定内存，每个socket就是一个文件描述符（Linux系统中，**万物皆文件**，所有的都是依赖于文件），另外1024以下的端口通常为保留端口。

对server端，通过增加内存、修改句柄个数等参数，单机最大并发TCP连接数超过10万 是没问题的，国外 Urban Airship 公司在产品环境中已做到 50 万并发 。在实际应用中，对大规模网络应用，还需要考虑C10K 问题。
