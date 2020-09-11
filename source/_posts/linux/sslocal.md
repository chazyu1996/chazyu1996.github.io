---
title: "linux 使用 shadowsocket 翻墙"
tags:
   - linux
categories:
  - 技术文章
thumbnail: /images/thumbnail/ladder.jpg
date: 2019-07-05T10:03:48+08:00
toc: true
---
# linux 使用 shadowsocket 翻墙

ss-local 是 shadowsocks 的本地 socks5 服务器，如果需要使用 ss-local 提供的 socks5 代理，必须让应用程序使用 socks5 协议与之通信。但是很可惜，除了部分浏览器、软件直接支持 socks5 协议外，其它的都只支持 http 代理。因此，我们需要借助 privoxy 来将 http 代理协议转换为 socks5 代理协议，与后端的 ss-local 进行通信，与此同时我们还可以进行 gfwlist 分流操作。
<!--more-->

### 安装 `ss-local`

`pip install shadowsocks`

> 配置ss-local
```json
{
    "server": "1.2.3.4",          # 服务器IP
    "server_port": 8989,          # 服务器Port
    "method": "aes-128-cfb",      # 加密方式
    "password": "123456",         # 端口密码
    "local_address": "127.0.0.1", # 本地监听IP
    "local_port": 1080,           # 本地监听Port
    "fast_open": true,            # TCP Fast Open
    "workers": 1                  # worker进程数量
}
```

> 启动ss-local
`sslocal -c /etc/ss-local.json`

### 安装 `privoxy`

`yum -y install privoxy`

> 配置 privoxy的config文件，配置监听端口

> 启动privoxy

`privoxy config`


### 在命令行配置 proxy

> `export http_proxy=http://127.0.0.1:${privoxy监听端口}`
> `export https_proxy=http://127.0.0.1:${privoxy监听端口}`
