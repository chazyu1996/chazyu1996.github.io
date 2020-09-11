---
title: overlay文件系统
date: 2020-04-02 23:21:36
tags:
  - linux
  - 文件系统
  - docker
categories:
  - 技术文章
toc: true
thumbnail: /images/thumbnail/file.jpg
---

Overlayfs是一种类似aufs的一种堆叠文件系统，于2014年正式合入Linux-3.18主线内核，是一种覆盖式的文件系统。它依赖并建立在其它的文件系统之上（例如ext4fs和xfs等等），并不直接参与磁盘空间结构的划分，仅仅将原来底层文件系统中不同的目录进行“合并”，然后向用户呈现。因此对于用户来说，它所见到的overlay文件系统根目录下的内容就来自挂载时所指定的不同目录的“合集”。
<!--more-->

常见用法是，底层文件系统只读，上层文件系统可读写；著名的docker就是使用的overlayfs。

在嵌入式应用中，底层只读系统一般使用squashfs，上层使用jffs2.

![overlay](/images/linux/filesystem/overlayFS1.jpg)

如图所示，底层目录为只读目录，当用户需要修改时，会把底层目录中的文件`cp`一份至上层目录，并进行修改、增加

## docker中的对应关系
> image layer  --> 底层目录
> container layer --> 上层目录
> container mount --> 合并目录

### 读
* 如果文件在容器层（upperdir），直接读取文件；
* 如果文件不在容器层（upperdir），则从镜像层（lowerdir）读取；

### 写
* 首次写入： 如果在upperdir中不存在，overlay和overlay2执行copy_up操作，把文件从lowdir拷贝到upperdir，由于overlayfs是文件级别的（即使文件只有很少的一点修改，也会产生的copy_up的行为），后续对同一文件的在此写入操作将对已经复制到容器的文件的副本进行操作。这也就是常常说的写时复制（copy-on-write）
* 删除文件和目录： 当文件在容器被删除时，在容器层（upperdir）创建whiteout文件，镜像层(lowerdir)的文件是不会被删除的，因为他们是只读的，但without文件会阻止他们显示，当目录在容器内被删除时，在容器层（upperdir）一个不透明的目录，这个和上面whiteout原理一样，阻止用户继续访问，即便镜像层仍然存在。 

### docker中的使用
1. 容器层的文件删除只是一个“障眼法”，是靠whiteout文件将其遮挡,image层并没有删除，这也就是为什么使用docker commit 提交保存的镜像会越来越大，无论在容器层怎么删除数据，image层都不会改变。

## 注意事项

1. 在进行`copy-up` 操作时，相对于磁盘类的文件系统，速度会大幅降低，为了保证底层目录用户间不互相影响，且保证一致性，`copy-up`时会进行很多操作，导致在复制大文件和多个小文件时慢
2. 不可rename 文件夹，可以通过mv 进行文件夹的修改，但是修改时，会创建完整副本，并不会仅仅修改inode 索引
3. 在硬链接中，当合并目录对底层目录的某个硬链接文件进行修改时，会在上层目录`copy-up`一份并进行修改，此时此文件和原始文件的硬链接就断开了，变成了一个单独的文件。
