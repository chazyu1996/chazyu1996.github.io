---
title: HTTPS工作流程
date: 2019-07-05T10:04:01+08:00
tags:
  - network
categories:
  - 技术文章
toc: true
---

# (普及)HTTPS工作流程 
<!--more-->

###  简单回顾

在前面两篇博客中介绍了密码相关的一些基本工具，包括（对称密码，公钥密码，密码散列函数，混合密码系统，消息认证码码，数字签名，伪随机数，数字证书）这几个。其中它们之间也是互相依赖的，我们来简单的梳理一下它们的依赖关系。

* 对称密码：无。
* 公钥密码：无。
* 密码散列函数：无。
* 伪随机数：可以利用密码散列函数来实现，也可以不使用。
* 混合密码系统：对称密码 + 公钥密码 + 密码散列函数。
* 消息认证码：密码散列函数 + 对称密码。
* 数字签名：密码散列函数 + 公钥密码。
* 数字证书：公钥密码 + 数字签名。

这篇要介绍的HTTPS，则把以上这些全都派上场了。

### HTTPS 简史

在早期HTTP诞生的这几年间，1990年~1994年，HTTP作为一个应用层协议，它是这样工作的:

![http工作模式](/images/network/http工作模式.png)

后来网景公司开发了SSL（Secure Sockets Layer）技术，然后它就变成了这样的HTTP，也就是HTTPS了：

![https工作模式](/images/network/https工作模式.png)


后来爆发了与IE的世纪大战，网景败北，SSL移交给了IETF（Internat Engineering Task Force）互联网工程任务组，标准化之后变成了现在的TLS，现在一般会把它们两个放在一起称为SSL/TLS。本篇并不关注SSL/TLS具体是如何工作的，只是抽象的解释下HTTPS的一个工作流程。

### HTTPS 工作流程

![https工作流程](/images/network/https工作流程.png)

1. Client发起一个HTTPS（https:/demo.linianhui.dev）的请求，根据RFC2818的规定，Client知道需要连接Server的443（默认）端口。
2. Server把事先配置好的公钥证书（public key certificate）返回给客户端。
3. Client验证公钥证书：比如是否在有效期内，证书的用途是不是匹配Client请求的站点，是不是在CRL吊销列表里面，它的上一级证书是否有效，这是一个递归的过程，直到验证到根证书（操作系统内置的Root证书或者Client内置的Root证书）。如果验证通过则继续，不通过则显示警告信息。
4. Client使用伪随机数生成器生成加密所使用的会话密钥，然后用证书的公钥加密这个会话密钥，发给Server。
5. Server使用自己的私钥（private key）解密这个消息，得到会话密钥。至此，Client和Server双方都持有了相同的会话密钥。
6. Server使用会话密钥加密“明文内容A”，发送给Client。
7. Client使用会话密钥解密响应的密文，得到“明文内容A”。
8. Client再次发起HTTPS的请求，使用会话密钥加密请求的“明文内容B”，然后Server使用会话密钥解密密文，得到“明文内容B”。

具体的格式可以参考MDN的一个说明https://developer.mozilla.org/en-US/docs/Mozilla/Projects/NSS/Key_Log_Format。

以上只是一个抽象的HTTPS的一个工作流程，实际上SSL/TLS所做的工作远不止这这些，更详细的解释请参考这篇文章：https://infoq.cn/article/HTTPS-Connection-Jeff-Moser

如有错误之处，欢迎指正！

### 参考

> SSL/TLS：https://en.wikipedia.org/wiki/Transport_Layer_Security

> IETF：https://en.wikipedia.org/wiki/Internet_Engineering_Task_Force

> HTTPS：https://en.wikipedia.org/wiki/HTTPS

> HTTPS 连接最初的若干毫秒：http://www.infoq.com/cn/articles/HTTPS-Connection-Jeff-Moser

> HTTPS on Stack Overflow: The End of a Long Road：https://nickcraver.com/blog/2017/05/22/https-on-stack-overflow/

> SSL/TLS部署最佳实践：https://github.com/ssllabs/research/wiki/SSL-and-TLS-Deployment-Best-Practices

> HTTP Over TLS：https://tools.ietf.org/html/rfc2818

> SSLKEYLOGFILE：https://developer.mozilla.org/en-US/docs/Mozilla/Projects/NSS/Key_Log_Format

