---
title: pip国内源
date: 2019-07-05T10:03:48+08:00
toc: true
tags:
  - python
categories:
  - 技术文章
---
<!--more-->

### pip国内源

> 阿里云 https://mirrors.aliyun.com/pypi/simple/

> 中国科技大学 https://pypi.mirrors.ustc.edu.cn/simple/ 

> 豆瓣(douban) https://pypi.douban.com/simple/ 

> 清华大学 https://pypi.tuna.tsinghua.edu.cn/simple/

> 中国科学技术大学 http://pypi.mirrors.ustc.edu.cn/simple/

### 如果是使用conda来安装， 执行这两条命令，可以将国内镜像源加入config文件

##### 添加

> conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/

> conda config --set show_channel_urls yes

##### 删除

> conda config --get channels 

> conda config --add channels https://mirrors.tuna.tsinghua.edu.cn/anaconda/pkgs/free/
