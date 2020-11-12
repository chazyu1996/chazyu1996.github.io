---
title: "iptables四个表与五个链"
date: 2020-11-12 20:02:19
tags:
  - linux
categories:
  - 技术文章
toc: true

---



netfilter/iptables IP 信息包过滤系统是一种功能强大的工具，可用于添加、编辑和除去规则，这些规则是在做信息包过滤决定时，防火墙所遵循和组成的规则。这些规则存储在专用的信息包过滤表中，而这些表集成在 Linux 内核中。在信息包过滤表中，规则被分组放在我们所谓的链（chain）中。
 <!--more-->

# netfilter和iptables

> netfilter 组件也称为内核空间（kernelspace），是内核的一部分，由一些信息包过滤表组成，这些表包含内核用来控制信息包过滤处理的规则集。

> iptables 组件是一种工具，也称为用户空间（userspace），它使插入、修改和除去信息包过滤表中的规则变得容易。

iptables包含4个表，5个链。其中表是按照对数据包的操作区分的，链是按照不同的Hook点来区分的，表和链实际上是netfilter的两个维度。

## 4个表

默认表是filter（没有指定表的时候就是filter表）。

> 表的处理优先级：raw>mangle>nat>filter。

###   filter

一般的过滤功能

### nat

用于nat功能（端口映射，地址映射等）

### mangle

用于对特定数据包的修改

### raw

有限级最高，设置raw时一般是为了不再让iptables做数据包的链接跟踪处理，提高性能

RAW 表只使用在PREROUTING链和OUTPUT链上,因为优先级最高，从而可以对收到的数据包在连接跟踪前进行处理。一但用户使用了RAW表,在某个链 上,RAW表处理完后,将跳过NAT表和 ip_conntrack处理,即不再做地址转换和数据包的链接跟踪处理了。

RAW表可以应用在那些不需要做nat的情况下，以提高性能。如大量访问的web服务器，可以让80端口不再让iptables做数据包的链接跟踪处理，以提高用户的访问速度。

##  5个链

### PREROUTING

数据包进入路由表之前

### INPUT

通过路由表后目的地为本机

### FORWARDING

通过路由表后，目的地不为本机

### OUTPUT

由本机产生，向外转发

### POSTROUTIONG

发送到网卡接口之前。


![流量走向](/images/linux/iptables/iptable-1.png)



## iptables的数据包的流程是怎样的？

![流量走向](/images/linux/iptables/iptable-2.png)

基本步骤如下： 
1. 数据包到达网络接口，比如 eth0。 
2. 进入 raw 表的 PREROUTING 链，这个链的作用是赶在连接跟踪之前处理数据包。 
3. 如果进行了连接跟踪，在此处理。 
4. 进入 mangle 表的 PREROUTING 链，在此可以修改数据包，比如 TOS 等。 
5. 进入 nat 表的 PREROUTING 链，可以在此做DNAT，但不要做过滤。 
6. 决定路由，看是交给本地主机还是转发给其它主机。 

到了这里我们就得分两种不同的情况进行讨论了，一种情况就是数据包要转发给其它主机，这时候它会依次经过： 
7. 进入 mangle 表的 FORWARD 链，这里也比较特殊，这是在第一次路由决定之后，在进行最后的路由决定之前，我们仍然可以对数据包进行某些修改。 
8. 进入 filter 表的 FORWARD 链，在这里我们可以对所有转发的数据包进行过滤。需要注意的是：经过这里的数据包是转发的，方向是双向的。 
9. 进入 mangle 表的 POSTROUTING 链，到这里已经做完了所有的路由决定，但数据包仍然在本地主机，我们还可以进行某些修改。 
10. 进入 nat 表的 POSTROUTING 链，在这里一般都是用来做 SNAT ，不要在这里进行过滤。 
11. 进入出去的网络接口。完毕。 

另一种情况是，数据包就是发给本地主机的，那么它会依次穿过： 
7. 进入 mangle 表的 INPUT 链，这里是在路由之后，交由本地主机之前，我们也可以进行一些相应的修改。 
8. 进入 filter 表的 INPUT 链，在这里我们可以对流入的所有数据包进行过滤，无论它来自哪个网络接口。 
9. 交给本地主机的应用程序进行处理。 
10. 处理完毕后进行路由决定，看该往那里发出。 
11. 进入 raw 表的 OUTPUT 链，这里是在连接跟踪处理本地的数据包之前。 
12. 连接跟踪对本地的数据包进行处理。 
13. 进入 mangle 表的 OUTPUT 链，在这里我们可以修改数据包，但不要做过滤。 
14. 进入 nat 表的 OUTPUT 链，可以对防火墙自己发出的数据做 NAT 。 
15. 再次进行路由决定。 
16. 进入 filter 表的 OUTPUT 链，可以对本地出去的数据包进行过滤。 
17. 进入 mangle 表的 POSTROUTING 链，同上一种情况的第9步。注意，这里不光对经过防火墙的数据包进行处理，还对防火墙自己产生的数据包进行处理。 
18. 进入 nat 表的 POSTROUTING 链，同上一种情况的第10步。 
19. 进入出去的网络接口。完毕。

## iptables raw表的使用

增加raw表，在其他表处理之前，-j NOTRACK跳过其它表处理
状态除了以前的四个还增加了一个UNTRACKED

例如：
可以使用 “NOTRACK” target 允许规则指定80端口的包不进入链接跟踪/NAT子系统

iptables -t raw -A PREROUTING -d 1.2.3.4 -p tcp --dport 80 -j NOTRACK
iptables -t raw -A PREROUTING -s 1.2.3.4 -p tcp --sport 80 -j NOTRACK
iptables -A FORWARD -m state --state UNTRACKED -j ACCEPT


## 解决ip_conntrack: table full, dropping packet的问题


在启用了iptables web服务器上，流量高的时候经常会出现下面的错误：

ip_conntrack: table full, dropping packet


这个问题的原因是由于web服务器收到了大量的连接，在启用了iptables的情况下，iptables会把所有的连接都做链接跟踪处理，这样iptables就会有一个链接跟踪表，当这个表满的时候，就会出现上面的错误。

iptables的链接跟踪表最大容量为/proc/sys/net/ipv4/ip_conntrack_max，链接碰到各种状态的超时后就会从表中删除。

所以解決方法一般有两个：

(1) 加大 ip_conntrack_max 值

vi /etc/sysctl.conf

net.ipv4.ip_conntrack_max = 393216
net.ipv4.netfilter.ip_conntrack_max = 393216


(2): 降低 ip_conntrack timeout时间

vi /etc/sysctl.conf

net.ipv4.netfilter.ip_conntrack_tcp_timeout_established = 300
net.ipv4.netfilter.ip_conntrack_tcp_timeout_time_wait = 120
net.ipv4.netfilter.ip_conntrack_tcp_timeout_close_wait = 60
net.ipv4.netfilter.ip_conntrack_tcp_timeout_fin_wait = 120


上面两种方法打个比喻就是烧水水开的时候，换一个大锅。一般情况下都可以解决问题，但是在极端情况下，还是不够用，怎么办？

这样就得反其道而行，用釜底抽薪的办法。iptables的raw表是不做数据包的链接跟踪处理的，我们就把那些连接量非常大的链接加入到iptables raw表。

如一台web服务器可以这样：

iptables -t raw -A PREROUTING -d 1.2.3.4 -p tcp --dport 80 -j NOTRACK
iptables -A FORWARD -m state --state UNTRACKED -j ACCEPT