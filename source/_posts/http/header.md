---
title: http 的header
tags:
  - http
categories:
  - 技术文章
date: 2019-07-05T10:03:48+08:00
toc: true
---
# http中的header
HTTP（HyperTextTransferProtocol） 即超文本传输协议，目前网页传输的的通用协议。HTTP协议采用了请求/响应模 型，浏览器或其他客户端发出请求，服务器给与响应。就整个网络资源传输而言，包括message-header和message-body两部分。首先传 递message-header，即http header消息 。http header 消息通常被分为4个部分：general  header, request header, response header, entity header。但是这种分法就理解而言，感觉界限不太明确。根据维基百科对http header内容的组织形式，大体分为Request和Response两部分。

<!--more-->

## 通用首部

| 字段名            | 说明                 |
| ----------------- | -------------------- |
| Cache-Control     | 控制缓存行为         |
| Connection        | 逐跳首部、连接的管理 |
| Data              | 创建报文的日期时间   |
| Pragma            | 报文指令             |
| Tailaer           | 报文末端的首部一览   |
| Transfer-Encoding | 报文传输的编码方式   |
| Upgrade           | 升级为其他协议       |
| Via               | 代理服务器的相关信息 |
| Warning           | 错误通知             |

## 请求首部

| 字段名               | 说明                                      |
| -------------------- | ----------------------------------------- |
| Accept               | 控制缓存行为                              |
| Accept-Charset       | 优先的字符集                              |
| Accept-Encoding      | 优先的内容编码                            |
| Accept-Language      | 优先的语言                                |
| Authentication       | web认证信息                               |
| Except               | 期待服务器特定行为                        |
| From                 | 用户的电子邮箱地址                        |
| Host                 | 请求资源所在的服务器                      |
| if-Match             | 比较实体标记（Etag)                       |
| if-None-Match        | 比较实体标记，与if-Match相反              |
| if-Modified-Since    | 比较资源更新时间                          |
| if-Range             | 资源未更新时发送实体Byte范围              |
| if-UNmodified-Since  | 比较资源更新时间，与if-Modified-Since相反 |
| Max-Forwards         | 最大传输逐跳数                            |
| Proxy-Authentication | 代理服务器认证信息                        |
| Range                | 实体字节范围                            |
| Referer              |  URL原始获取方                           |
| TE              |  传输编码优先级                           |
| User-Agent              |  http客户端程序的信息              |

## 响应头部
|字段|说明|
|---|---|
|Accept-Ranges|是否接受字节范围请求|
|Age|资源创建经过时间|
|Etag|资源匹配信息|
|Location|客户端重定向至指定url|
|Proxy-Authentication|代理服务器对客户端的认证信息|
|Retry-After|对再次发起请求的时机的要求|
|Server|HTTP服务器安装信息|
|Vary|代理服务器缓存的管理信息|
|WWW-Authentication|服务器对客户端的认证信息|

## 实体头部
|字段|说明|
|--|--|
|Allow|资源支持的http方法|
|Content-Encoding|实体使用的编码|
|Content-Language|实体主题的语言|
|Content-Length|实体大小（字节）|
|Content-Location|替代资源URL|
|Content-MD5|实体报文摘要|
|Content-Range|实体主体位置范围|
|Content-Type|实体主体媒体类型|
|Expires|实体主体的过期时间|
|Last-Modified|资源最后修改时间|