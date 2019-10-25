---
title: Docker
date: 2019-06-19 22:50:57
tags:
---


## Abstract
Docker是一个开源的容器引擎，可以将应用软件和应用所需的依赖打包在一起，形成一个标准化单元，类似于Java基于jvm的*write once, run anywhere*， docker可以实现*build once, run anywhere*。最近几年，微服务架构火热，而在大型企业中，微服务的运维由于服务的数量比较多而进展困难，容器化服务势在必行。服务容器化使应用程序能够从组件快速组装到发布，消除了开发和QS和IT的摩擦。容器是微服务的基石，没有容器化的微服务是没有灵魂的。docker让容器化操作变得简单，docker的出现让大型的微服务架构得以流行，而微服务的火热又促进了docker发展。


## Why
### 一致的运行环境
> 开发过程中一个常见的问题是环境一致性问题。由于开发环境、测试环境、生产环境不一致，导致有些 bug 并未在开发过程中被发现。而 Docker 的镜像提供了除内核外完整的运行时环境，确保了应用运行环境一致性，从而不会再出现 「这段代码在我机器上没问题啊」 这类问题。
### 持续交付和部署
> 对开发和运维（DevOps）人员来说，最希望的就是一次创建或配置，可以在任意地方正常运行。
> 使用 Docker 可以通过定制应用镜像来实现持续集成、持续交付、部署。开发人员可以通过 Dockerfile 来进行镜像构建，并结合 持续集成(Continuous Integration) 系统进行集成测试，而运维人员则可以直接在生产环境中快速部署该镜像，甚至结合 持续部署(Continuous Delivery/Deployment) 系统进行自动部署。
> 而且使用 Dockerfile 使镜像构建透明化，不仅仅开发团队可以理解应用运行环境，也方便运维团队理解应用运行所需条件，帮助更好的生产环境中部署该镜像。
### 更轻松的迁移
> 由于 Docker 确保了执行环境的一致性，使得应用的迁移更加容易。Docker 可以在很多平台上运行，无论是物理机、虚拟机、公有云、私有云，甚至是笔记本，其运行结果是一致的。因此用户可以很轻易的将在一个平台上运行的应用，迁移到另一个平台上，而不用担心运行环境的变化导致应用无法正常运行的情况。

### 更轻松的维护和扩展
> Docker 使用的分层存储以及镜像的技术，使得应用重复部分的复用更为容易，也使得应用的维护更新更加简单，基于基础镜像进一步扩展镜像也变得非常简单。此外，Docker 团队同各个开源项目团队一起维护了一大批高质量的 官方镜像，既可以直接在生产环境使用，又可以作为基础进一步定制，大大的降低了应用服务的镜像制作成本。

## VM, Vagrant, Docker
最开始，大家就想着直接把整个系统打包再部署，即虚拟机技术，保证了系统和环境的稳定，减少了大量手工操作。但是，缺点也是有的，那就是无法自动化打包过程，于是[Vargrant](https://www.vagrantup.com/)被做了出来，Vargrant使用了vargrantfile文件给开发者提供了定义自动化构建VM镜像的功能。但是，还是有缺点，首先，打包出来的镜像太大，包含了过多不需要使用的依赖；其次，服务启动必须等得VM的系统启动完成后才能启动服务。
为了解决VM的缺点，Docker被开发了出来，它提供了系统上的进程级别的虚拟化技术。与VM需要虚拟化整个硬件和操作系统不同，Docker是直接基于宿主机的操作系统进行虚拟化，性能几乎没有损耗。具体结构差异如下：
```
虚拟机：
    +------------+-------------+
    | App A      |  App B      |
    +------------+-------------+
    | Libs       |  Libs       |
    +------------+-------------+
    |Guest OS    |  Guest OS   |
    +------------+-------------+
    |       Hypervisor         |
    +--------------------------+
    |        HOST OS           |
    +--------------------------+
    |         SERVER           |
    +--------------------------+

Docker：
    +------------+-------------+
    | App A      |  App B      |
    +------------+-------------+
    | Libs       |  Libs       |
    +------------+-------------+
    |       Docker Engine      |
    +--------------------------+
    |        HOST OS           |
    +--------------------------+
    |         SERVER           |
    +--------------------------+

```

## Docker的组成
docker主要由以下组件组成：
- docker client
- docker daemon
- docker image
- docker container
- docker registry

Docker本身是C/S架构，C端是docker client，S端是docker daemon，docker daemon管理者docker image和docker container，docker index提供镜像索引以及用户认证的功能，docker registry在远程存放镜像，大致架构如下：
```


                           +-----------------+
                           | Docker Daemon   |
        ,---.              |  -------------- |                )------------------(
       /     \             |  Container1     |                |                  |
      / Docker\            |                 |--------------- |  Docker Registry |
     (  Client )---------- |  Container2     |                |                  |
      \       /            |                 |                )------------------(
       \     /             |  Container3     |                          |
        `---'              |                 |                          |
     docker pull           |  .......        |                          |
     docker run            |                 |                          |
     docker build          +-----------------+                          |
     docker ...                     |                                   |
                                    |                                   |
                            +----------------+                          |
                            |  Docker Index  |--------------------------+
                            +----------------+
```

### Docker Image

docker image是docker容器的基石，保存了docker启动的各种条件，docker container的启动必须基于一个docker image。Docker image位于docker生命周期的构建和打包阶段。
docker使用了union mount的方式构建镜像，采用了ufs的模型。就是docker一次性加载多个文件系统形成一个文件系统层叠的形式，但是在外部看来容器只有一个文件系统可用，docker将这样的文件系统称为docker image。
docker image是一个层层叠加的只读文件系统。最底部是bootfs，即引导文件系统，当容器启动后，引导文件系统将会被卸载。bootfs之上是rootfs，rootfs可以是ubuntu/centos/debian/fedora，与虚拟机启动后由只读状态变为读写状态不同，docker的rootfs永远是只读。
```
Docker的UFS示例：
 +---------------+
 |  add django   |
 +---------------+
 |  add python3  |
 +---------------+
 |rootfs(ubuntu) |
 +---------------+
 |     bootfs    |
 +---------------+

```


### Docker Container
Docker Container是Docker的执行单元，通过镜像来启动。Docker container位于docker生命周期的启动和执行阶段。
Docker Container基于镜像启动时，会在镜像构建出的ufs上再添加一个writable layer，一个可写层。当容器刚启动时，该读写层是空的，当容器状态发生改变时，读写层才会有变化。比如当需要修改一个文件时，docker把改文件从下层的只读文件系统中复制到读写层，再进行修改，该文件的只读版本仍然存在，但是已经被读写层上的副本隐藏起来了。这就是Docker中重要的技术**Copy on Write**（中文翻译为写时复制），每个只读镜像层永远都是只读的，不会变化，当创建出一个新容器时，docker会构建出一个镜像栈，在栈的顶层增加读写层，并与底下的只读层和一些数据就构成了一个容器。容器的分层框架能够使我们快速的构建镜像并发布应用程序。
```
Docker container创建的镜像栈

 +---------------+
 | add my app    |  读写层(copy on write)
 +---------------+
 |  add django   |  Read Only
 +---------------+
 |  add python3  |  Read Only
 +---------------+
 |rootfs(ubuntu) |  Read Only
 +---------------+
 |     bootfs    |  Read Only
 +---------------+

```

### Docker Registry
Docker仓库分为公有和私有，放着一些已有的镜像，官方的docker registry是[Docker Hub](https://www.docker.com/products/docker-hub)。



### Namespaces
C++中有namespace这个概念，它实现了代码块的封装，更重要的是代码的隔离，使得不同namespace下可以有相同的变量名但意义不同。在linux里，namespace是一个重要的特性，提供了系统资源的隔离，包括进程/网络/文件系统等。linux实现namespace的重要目的就是为了实现轻量级的虚拟化，也就是容器（linux里的lxc了解一下）。在同一namespace下的进程可以感知彼此的变化，而不能发现其他进程，从而使得一个namespace下的进程认为自身处于一个独立的系统中，便达到了独立和隔离的目的。
Docker目前用到了以下几种namespace进行资源隔离。
- PID， Process ID, 进程隔离；
- NET， Network， 网络隔离；
- IPC， InterProcess Communication， 跨进程通信访问隔离；
- MNT， Mount， 挂载点隔离；
- UTS， Unix Timesharing System， 内核和版本标识隔离；
- USER, 用户和组隔离；

而隔离资源的管理就需要用到Control Groups

### Control Groups
Control groups是linux提供的限制/记录/隔离主机物理资源的机制，简称cgroups，为了更好的管理容器，cgroups在2007年并入到linux kernel中。没有cgroups，就没有容器。
cgroups主要负责容器相关的以下内容：
- 资源限制，比如为进程组设置一个内存使用的上限；
- 优先级设定，比如为某些进程组设定更大的cpu和内存资源。
- 资源计量，比如计算进程组使用了多少系统资源。
- 资源控制，比如将进程组挂起和恢复。

正是因为namespaces和cgroups，Docker的容器才拥有了以下能力：
- 文件系统隔离：每个容器都有自己的rootfs；
- 进程隔离： 每个容器都运行在自己的进程环境中；
- 网络隔离： 容器间的虚拟网络接口和IP地址都是分离的；
- 资源隔离和分组： cgroups将CPU和内存等资源独立分配给每个不同的Docker容器。

### 容器的状态
- 运行
- 已暂停
- 重新启动
- 已退出

## Command

```bash
$ docker help

$ docker help cp
```



```bash
$ docker run --detach --interactive --tty --name web busybox:latest /bin/bash

$ docker run -d -name wp3 --link wpdb:mysql -p 80 -v /run/lock/apache2 -v /run/apache2 --read-only wordpress:4

$ docker run -d --rm --name wp --env MY_ENVIRONMENT_VAR="test" --read-only wordpress:4

$ docker inspect --format "{{.State.Running}}" wp

$ docker ps

$ docker top web

$ docker run --it

$ docker restart web

$ docker logs web

$ docker exec web ps

$ docker stop xxxx

$ docker search postgres

$ docker save -o myfile.tar busybox:latest

$ docker load -i myfile.tar

$ docker rmi busybox
```

## Dockerfile指令


类似于Makefile， Docker可以根据Dockerfile来构建镜像。Dockerfile是一个文本文件，每一行都是一个Dockerfile指令。接着使用docker build命令就可以构建出一个docker镜像了。Dockerfile是自动化运维的关键。

### 指令格式
注释
```
# Comment 这是注释
```
指令
```
INSTRUCTION arguments
```

样例
```
FROM ubuntu:16:04
LABEL author=fwz version=1.0.1
WORKDIR /root/myapp
COPY myapp .
RUN apt update \
    && apt install -y python3 python3-pip
    && pip3 install -r requirement.txt
EXPOSE 8000
ENTRYPOINT ["/usr/bin/python3", "/root/app/manage.py", "runserver"]
```

### FROM
FROM指令指定了一个基础镜像，接下来的指令都是基于这个基础镜像进行构建。每个Dockerfile都必须包含FROM指令。如果FROM指定的基础镜像不在本地，则会从Dockerhub上拉取。
```
FROM ubuntu:16:04
```

```
FROM python:3.7
```

### RUN
RUN指令是构建时执行的指令，。格式是`RUN <command>`或者`RUN ['executable', 'param1', 'param2', ...]`，RUN指令默认使用shell执行命令，即`/bin/sh -c`执行命令，如果想使用不同的shell，比如`bash`，那么可以像这么写：
```
RUN ['/bin/bash', '-c' 'python3 bootstrap.py']
```
而且最好RUN指令的内容都写在同一行，防止ufs的层数过多。

### CMD
CMD指令是容器启动时执行的指令，可以包含参数，比如：
```
CMD ["python3", "manage.py", "runserver", "0.0.0.0:8000"]
```

### EXPOSE
用于指定容器监听的内部端口号，不过可以在`docker run`的时候更改端口映射；
```
EXPOSE 27017
```

### COPY
把文件从本地复制到容器中
```
COPY application.yml /app/
```

### ENTRYPOINT
和CMD类似，也是容器启动时执行的命令，不过可以在容器启动即`docker run`时提供参数
```
ENTRYPOINT ["java", "app.jar"]
```

### LABEL
记录容器相关的变量，`docker inspect`时可以看到
```
LABEL author="菜徐坤" version="7.7.7"
```