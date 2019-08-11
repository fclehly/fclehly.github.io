---
title: HDFS
date: 2019-08-06 22:18:47
tags:
- hadoop
categories:
- note
---

# HDFS

## 存储模型

文件线性切割成块（block），每个block可以根据其字节内容在原文件中的偏移量offset确定，即第一个block的第一个字节的offset为0、第二个block的第一个字节的offset为block_size。block分散存储到集群节点当中，单一文件的block大小一致，不同文件之间的block大小可以不一样。block可以设置副本数，block的副本分散在不同节点中（即副本不会出现在同一节点中），这样可以很好地支持多个程序处理文件，但副本数不能超过节点数量。文件上传可以设置block的大小和副本数，且已上传的文件block副本数可以调整，但是大小不能改变。只支持一次写入多次读取，同一时刻只有一个写入者。可以append追加数据，但不可以修改block。

## 架构模型

文件组成分为：
- 元数据metadata，
- 文件数据

主从架构：
- NameNode节点保存文件元数据：单节点，维护一个虚拟目录树，映射到DataNode的文件系统的Block。
- DataNode节点保存文件block数据：多节点；

![hdfs-architecture](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/images/hdfsarchitecture.png)

DataNode和NameNode保持心跳，提交block列表；

HdfsClient和NameNode交互元数据信息；

HdfsClient和DataNode交互文件Block信息。

HdfsClient与NameNode交互后，向DataNode交互，NameNode起到了一定的负载均衡作用。

### NameNode

基于内存存储，不会和磁盘发生交换。数据只存在内存中，但会通过快照或增量日志做持久化策略。

主要功能：
* 接受客户端的读写服务；
* 收集DataNode汇报的Block列表信息。

其保存的metadata信息包括：
* 文件ownership， permissions；
* 文件大小，时间
* block列表，block偏移量，位置信息；
* block每个副本的位置（由DataNode上报）

### NameNode持久化
NameNode的metadata在启动后会加载到内存；

metadata存储到磁盘的文件名为fsimage；

block的位置信息不会保存到fsimage；

edits记录对metadata的操作记录；

### DataNode

本地磁盘目录存储数据block，使文件形式，同时存储block的元数据信息文件幂md5；

启动datanode时会向namenode汇报block信息；

通过向nanenode发送心跳保持与其联系（3秒1次），如果namenode在10分钟内没有收到datanode的信息，则认为其已经lost，并copy其上的block（从其他节点的副本拷贝扩散，保证副本数一致）到其他datanode。

### HDFS优点
- 高容错性：
  - 数据自动保存多个副本；
  - 副本数据丢失，则会自动回复；
- 适合批处理
  - 移动计算程序而不是数据；
  - 数据位置暴露给计算框架；
- 适合大数据处理
  - GB、TB、PB
  - 百万规模以上的文件数量
  - 10k+节点
- 可构建在廉价机器上（可靠性越高的机器越贵）
  - 通过多副本提高可靠性；
  - 提供了容错和恢复机制；


### HDFS缺点
- 低延迟数据访问
  - 毫秒级随机访问
  - 低延迟与高吞吐率
- 小文件存取
  - 占用NameNode大量内存
  - 寻道时间超过读取时间
- 并发写入、文件随机修改
  - 一个文件只能有一个写者
  - 仅支持append