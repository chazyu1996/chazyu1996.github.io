---
title: 网卡Bond
date: 2019-07-05T10:02:02+08:00
tags:
  - linux
summary: 网卡Bond
categories:
  - 技术文章
thumbnail: /images/thumbnail/network-switch.jpg
toc: true
---

## 什么是bond？

所谓bond，是一种通过把多个物理网卡绑定成一个逻辑网卡实现网卡冗余、负载均衡、提高带宽，从而实现网络性能高可用高可靠的技术。
<!--more-->

## bond的七种模型：

>mod0：（balance-rr，平衡轮循环策略，提供负载均衡和容错能力），数据包传输是依次传输，第一个包从网卡1传输，第二个包从网卡2传输，第三个包从网卡3.......，一直循环直到传输完最后一个数据包。这种模式的bond有一个不完善的地方，如果一个数据包从不同的网卡传输，而中途再经过不同的链路，当客户端接受到数据包的时候，数据包就可能出现无序到达的情况，而无序到达的数据包需要重新发送，这样网络的性能便会大大下降。

>mod1：（active-backup，主备策略，提供冗余能力），只有一个网卡被使用，当一个网卡宕了之后，马上由备网卡接替主网卡的工作，为了避免交换机发生混乱，逻辑网卡的mac地址是唯一的。这种模型的bond可提高网络的可用性，但是它的资源利用率低，只有1/网卡个数（N）。

>mod2：（balance-xor，平衡策略，提供负载均衡和容错能力）---不是很明白实现原理与算法，有哪位大神知道的话，可以在下面留言，让小弟也开开眼界。

>mod3：（broadcast，广播策略，提供容错能力）每一个备网卡传输每个数据包。

>mod4：（802.3ad，动态链路聚合），创建聚合组，聚合组中的每个备网卡共享同样的速率和双工，必要条件是交换机需要支持802.3ad以及需要ethtool的支持

>mod5：（balance-tlb，适配器传输负载均衡），在每个网卡上根据当前的压力负载分配流量，如果正在工作的网卡宕了，另外的网卡接管宕机的网卡的mac地址。必要条件是：需要ethtool的支持。

>mod6：（balance-alb，适配器适应负载均衡），该模式包含了balance-tlb模式，同时加上针对IPV4流量的接收负载均衡(receive load balance, rlb)，而且不需要任何switch(交换机)的支持。接收负载均衡是通过ARP协商实现的。bonding驱动截获本机发送的ARP应答，并把源硬件地址改写为bond中某个slave的唯一硬件地址，从而使得不同的对端使用不同的硬件地址进行通信。



### 实验（mod0为例，其他mod大同小异）

创建逻辑网卡的配置文件

```shell
# 创建逻辑网卡的配置文件
[root@bond network-scripts]# catifcfg-bond0   

DEVICE=bond0

BOOTPROTO=static

ONBOOT=yes

IPADDR=192.168.31.100

NETMASK=255.255.255.0

NETWORK=192.168.31.0

GATEWAY=192.168.31.1

BROADCAST=192.168.31.255

BONDING_OPTS="mode=0  miimon=200"  #mode指定模式，miimon为探测的时间间隔(毫秒) 

USERCTL=no     #是否允许非root用户控制该设备  yes|no
```

```shell
# 修改物理网卡的配置文件
[root@bond network-scripts]# catifcfg-eno16780032
DEVICE=eno16780032
BOOTPROTO=none
MASTER=bond0    #指定master为bond0
SLAVE=yes            #是否为附属
USERCTL=no

[root@bond network-scripts]# catifcfg-eno33561344
DEVICE=eno33561344
BOOTPROTO=none
MASTER=bond0   
SLAVE=yes
USERCTL=no
```

```shell
# bond0是通过bonding的驱动来配置的，所以我们还需要为bond0这块网卡添加驱动支持，将这个驱动添加到 /etc/modprobe.d/ 这个目录下
[root@bond ~]# cat/etc/modprobe.d/bonding.conf
alias bond0 bonding
```

```shell
#加载bonding模块
[root@bond ~]# modprobe  bonding
[root@bond ~]# lsmod |grep bonding
bonding               136705  0
```

```shell
#重启网络
[root@bond ~]# systemctl restart network
Job for network.service failed because thecontrol process exited with error code. See "systemctl statusnetwork.service" and "journalctl -xe" for details.
 
bond network[10418]:Bringing up interface bond0:  Error:Connection activation failed: No suitable device found for this connection.
bond network[10418]:Bringing up interface eno16780032: Error: Connection activation failed: Master device bond0 unmanaged ornot available for activation
```

```shell
#如果发现有如上报错，需要关闭NetworkManager。
参考（https://access.redhat.com/discussions/2162171）
[root@bond ~]# systemctl stopNetworkManager
[root@bond ~]# systemctl disabledNetworkManager
```

```shell
#验证       仔细观察发现物理网卡的mac地址和逻辑网卡的mac地址一样
[root@bond ~]# ip add show
3: eno16780032: <BROADCAST,MULTICAST,SLAVE,UP,LOWER_UP>mtu 1500 qdisc mq master bond0 state UP qlen1000
   link/ether 00:0c:29:bc:7d:41 brdff:ff:ff:ff:ff:ff
   inet6 fe80::20c:29ff:febc:7d41/64 scopelink
      valid_lft forever preferred_lft forever
4: eno33561344: <BROADCAST,MULTICAST,SLAVE,UP,LOWER_UP>mtu 1500 qdisc mq master bond0 state UP qlen1000
   link/ether 00:0c:29:bc:7d:41 brdff:ff:ff:ff:ff:ff
   inet6 fe80::20c:29ff:febc:7d41/64 scopelink
      valid_lft forever preferred_lft forever
5: bond0:<BROADCAST,MULTICAST,MASTER,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP
   link/ether 00:0c:29:bc:7d:41 brdff:ff:ff:ff:ff:ff
   inet 192.168.31.100/24 brd 192.168.31.255 scope global bond0
      valid_lft forever preferred_lft forever
   inet6 fe80::20c:29ff:febc:7d41/64 scopelink tentative dadfailed
      valid_lft forever preferred_lft forever
```

```shell
#查看逻辑网口设备的信息
[root@bond ~]# cat /proc/net/bonding/bond0
Ethernet Channel Bonding Driver: v3.7.1(April 27, 2011)
 
Bonding Mode: load balancing (round-robin)    #模式为负载均衡
MII Status: up      #状态为up
MII Polling Interval (ms): 200     #侦测间隔为200ms
Up Delay (ms): 0     #启动延迟为0ms
Down Delay (ms): 0   #关闭延迟为0ms
 
Slave Interface: eno16780032
MII Status: up     #状态up
Speed: 10000 Mbps    #速率为10000
Duplex: full       #全双工
Link Failure Count: 0   
Permanent HW addr: 00:0c:29:bc:7d:41  
Slave queue ID: 0
 
Slave Interface: eno33561344
MII Status: up
Speed: 10000 Mbps
Duplex: full
Link Failure Count: 0
Permanent HW addr: 00:0c:29:bc:7d:4b
Slave queue ID: 0
```

