---
title: "镜像中的时区问题"
date: 2021-02-05 21:27:02
tags:
  - docker 
categories:
  - 踩坑遇雷
toc: true
---


在容器的使用过程中，尤其是基础镜像，很多都是以UTC为准，但是再实际使用过程中，在获取时间时，通过调用系统时钟获取当前时间，统一在环境中把时间改为CST时间

<!--more-->

## alpha

```
FROM alpine:3.9

# 设置时区为上海
RUN apk add tzdata && \
    cp /usr/share/zoneinfo/Asia/Shanghai /etc/localtime && \
    echo "Asia/Shanghai" > /etc/timezone 
```