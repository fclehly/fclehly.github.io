---
title: Docker
date: 2019-06-19 22:50:57
tags:
---


类似于Makefile， Docker可以根据Dockerfile来构建镜像。Dockerfile是一个文本文件，每一行都是一个Dockerfile指令。接着使用docker build命令就可以构建出一个docker镜像了。

## Dockerfile指令

### FROM
FROM指令指定了一个基础镜像，接下来的指令都是基于这个基础镜像进行构建。每个Dockerfile都必须包含FROM指令。如果FROM指定的基础镜像不在本地，则会从Dockerhub上拉取。
```
FROM ubuntu:16:04
```

```
FROM python:3.7
```

### RUN
RUN指令是容器启动时执行的指令，位于容器里ufs的最顶层。格式是`RUN <command>`或者`RUN ['executable', 'param1', 'param2', ...]`，RUN指令默认使用shell执行命令，即`/bin/sh -c`执行命令，如果想使用不同的shell，比如`bash`，那么可以像这么写：
```
RUN ['/bin/bash', '-c' 'python3 bootstrap.py']
```