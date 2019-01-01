---
title: Points on Serverless
date: 2019-01-01 13:09:00
tags:
- serverless
- function
categories:
- Blog
---

# 关于Serverless架构

## Abstract
最近写的项目其中一个功能点用到了阿里云的函数计算（function computing， 简称fc）。fc属于[serverless computing](https://en.wikipedia.org/wiki/Serverless_computing)架构模式，是一种新兴的云计算架构，在云计算中也被描述为Faas（Function as a Service）,在2014年Amzon首先推出了AWS Lambda，之后各大主流云厂商跟进，在2016年也分别推出了自家的serverless服务，比如阿里的fc和腾讯的scf等。
> 阿里云函数计算是事件驱动的全托管计算服务。通过函数计算，您无需管理服务器等基础设施，只需编写代码并上传。函数计算会为您准备好计算资源，以弹性、可靠的方式运行您的代码，并提供日志查询、性能监控、报警等功能。借助于函数计算，您可以快速构建任何类型的应用和服务，无需管理和运维。而且，您只需要为代码实际运行所消耗的资源付费，代码未运行则不产生费用。

## 什么是serverless computing
### 概念
serverless computing是指开发者在构建和运行应用时无需管理服务器等基础设施。应用被解耦为细粒度的函数，函数是部署和运行的基本单位。用户只为实际使用的资源付费。serverless computing能够帮助应用开发者摆脱服务器等底层基础设施管理的负担，专注于业务层的创新。**Serverless不是真的不需要服务器了,而是不用过多的关注服务器**。在微服务的架构下，系统被拆分为一系列独立的服务，serverless的粒度更小，更组件化。

serverless函数是基于容器运行的，每个函数实例的运行时环境都由容器提供；每一次函数被调用都会serverless里的调度器都会首先首先查找可用容器，没有可用容器则会创建一个容器并拉取代码来运行函数，这就是冷启动，冷启动比较耗时。

### 发展

计算的发展演进：

```
物理机 --> 虚拟机 --> 云计算 --> 容器 --> serverless

```
云计算：
```
-------------
|   FaaS    |
-------------
|   Saas    |
-------------
|   Pass    |
-------------
|   IaaS    |
-------------
```

从大型物理机到通过虚拟化技术把物理机虚拟成单个的VM资源，从虚拟化集群到把集群搬到云计算上只做简单运维，再到把每一个VM按照运行空间最小化切分成更细的Docker容器，再从Doceker容器变成直接不用管理任何运行环境的Serverless服务。

### 优势
1. **节约使用成本**; 虚拟机通常并不能满负载运行，有一些资源浪费。
2. **简化设备运维**; 无需维护基础设施；
3. **实现快速上线**; 部署只需要几步操作；
4. **降低开发成本**; 只需要关注业务代码（java除外）和相关组件；
5. **自动扩展能力**;

### 劣势
1. **缺乏调试和开发工具**;
2. **不适合长时间运行应用**; serverless函数都有最长运行时间限制；
3. **完全依赖云厂商**;
4. **冷启动时间**; serverless函数第一次启动非常耗时；




## Reference
[serverless-computing-wikipedia](https://en.wikipedia.org/wiki/Serverless_computing)

[serverless.com](https://serverless.com/)

[Serverless 架构应用开发指南](https://github.com/phodal/serverless)

[函数计算-阿里云](https://help.aliyun.com/product/50980.html?spm=a2c4g.11186623.6.540.5e6f29e96KK0Ji)