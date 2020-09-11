---
title: pip安装包报错及解决方案
date: 2019-07-05T10:03:48+08:00
toc: true
tags:
- python
categories:
  - 踩坑遇雷
---
<!--more-->

#### Python-ldap 报错

```
yum install python-devel openldap-devel
```

#### flower 查看节点失败
因为tornado的新版本与flower的兼容性较低，不能查看节点信息
`pip install tornado==4.5.2`
