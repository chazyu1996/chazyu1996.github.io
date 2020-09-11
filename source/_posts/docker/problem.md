---
title: "docker使用过程中遇到的一些问题"
date: 2019-07-19T11:27:02+08:00
tags:
  - docker 
categories:
  - 踩坑遇雷
toc: true
---
<!--more-->

# docker 通过http连接私有仓库

1. 需要在 `/lib/systemd/system/docker.service` 启动文件增加配置 `--insecure-registry={ip}`
2. reload 系统启动项 `systemctl daemon-reload`
3. 重启docker `systemctl restart docker`

即可在使用私有仓库 `docker login -u {user} -p {password} {ip}`

