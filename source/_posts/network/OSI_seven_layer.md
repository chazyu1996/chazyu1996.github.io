---
title: OSI七层协议模型、TCP/IP四层模型
date: 2019-07-05T10:03:20+08:00
tags:
  - network
categories:
  - 技术文章
thumbnail: /images/thumbnail/network-switch.jpg
toc: true
---

# OSI七层协议模型、TCP/IP四层模型
<!--more-->

### OSI七层和TCP/IP四层的关系

1.  OSI引入了服务、接口、协议、分层的概念，TCP/IP借鉴了OSI的这些概念建立TCP/IP模型。
2.  OSI先有模型，后有协议，先有标准，后进行实践；而TCP/IP则相反，先有协议和应用再提出了模型，且是参照的OSI模型。
3.  OSI是一种理论下的模型，而TCP/IP已被广泛使用，成为网络互联事实上的标准。

> TCP：transmission control protocol 传输控制协议
> UDP：user data protocol 用户数据报协议

<table>
	<thead>
		<tr>
			<th>OSI 七层网络模型 </th>
			<th>TCP/IP 四层概念模型 </th>
			<th> </th>
		</tr>
	</thead>
	<tbody>
		<tr>
			<td>应用层（Application）</td>
			<td rowspan="3">应用层 </td>
			<td>HTTP、TFTP, FTP, NFS, WAIS、SMTP </td>
		</tr>
		<tr>
			<td>表示层（Presentation）</td>
			<td>Telnet, Rlogin, SNMP, Gopher </td>
		</tr>
		<tr>
			<td>会话层（Session）</td>
			<td>SMTP, DNS </td>
		</tr>
		<tr>
			<td>传输层（Transport）</td>
			<td>传输层 </td>
			<td> TCP, UDP</td>
		</tr>
		<tr>
			<td>网络层（Network）</td>
			<td> 网络层</td>
			<td> IP, ICMP, ARP, RARP, AKP, UUCP</td>
		</tr>
		<tr>
			<td>数据链路层（Data Link）</td>
			<td rowspan="2">数据链路层 </td>
			<td> FDDI, Ethernet, Arpanet, PDN, SLIP, PPP</td>
		</tr>
		<tr>
			<td>物理层（Physical）</td>
			<td>IEEE 802.1A, IEEE 802.2到IEEE 802.11 </td>
		</tr>
	</tbody>
</table>


### OSI七层协议模型

七层结构记忆方法：应、表、会、传、网、数、物

应用层协议需要掌握的是：HTTP（Hyper text transfer protocol）、FTP（file transfer protocol）、SMTP（simple mail transfer rotocol）、POP3（post office protocol 3）、IMAP4（Internet mail access protocol）

![图 1 ](/images/network/图1.jpeg)

### TCP/IP四层模型

![图 2 ](/images/network/图2.jpeg)

#### 数据包说明

![图 3 ](/images/network/图3.gif)
![图 4 ](/images/network/图4.jpeg)
