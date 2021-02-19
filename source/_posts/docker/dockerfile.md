---
title: "dockerfile 的编写注意事项"
date: 2020-07-19 21:27:02
tags:
  - docker 
categories:
  - 踩坑遇雷
toc: true
---


在Dockerfile的编写中，可能会遇到各式各样的问题，列举了常见的几个注意事项

<!--more-->

# CMD 和 ENTRYPOINT

CMD 和 ENTRYPOINT 指令都是用来指定容器启动时运行的命令。



## exec 模式和 shell 模式

### exec 模式

使用 exec 模式时，容器中的任务进程就是容器内的 1 号进程

> 在docker场景下，当执行`docker stop` 或 `docker kill` 命令时，都是向进程id为**1** 的进程发送对应的signal信号

```
FROM ubuntu
CMD [ "top" ]
```

exec 模式的特点是不会通过 shell 执行相关的命令，所以像 $HOME 这样的环境变量是取不到

```
FROM ubuntu
CMD [ "echo", "$HOME" ]
```

> 输出 `$HOME`

通过 exec 模式执行 shell 可以获得环境变量

```
FROM ubuntu
CMD [ "sh", "-c", "echo $HOME" ]
```

> 输出 `/root`

### shell 模式

使用 shell 模式时，docker 会以 /bin/sh -c "task command" 的方式执行任务命令。也就是说容器中的 1 号进程不是任务进程而是 bash 进程

```
FROM ubuntu
CMD top
```
## ENTRYPOINT

ENTRYPOINT 指令的目的也是为容器指定默认执行的任务。

## CMD 

当只有CMD时，作为默认的执行命令

当同时存在 ENTRYPOINT 和 CMD时，作为ENTRYPOINT 的传参