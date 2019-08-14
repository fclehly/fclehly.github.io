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


### SecondaryNameNode    


SNN不是NN的备份（但是可以做备份），其主要工作是帮助NN合并edits log，减少NN的启动时间。
SNN执行合并时间：
- 根据配置文件设置的时间间隔`fs.checkpoint.period`，默认为3600s；
- 根据配置文件设置edits log大小`fs.checkpoint.size`规定edits log文件的最大值默认是64MB

### 安全模式
- namenode启动时，首先将映像文件fsimage载入内存，并执行编辑日志（edits）中的各项操作。
- 一旦在内存中成功建立文件系统元数据的映射，则创建一个新的fsimage文件（这个操作不需要SecondaryNameNode）和一个空的编辑日志，这里也是namenode唯一一次创建fsimage。
- 此刻namenode运行在安全模式。即namenode的文件系统对于客户端来说只是只读的。（显示目录、文件内容等。写、删除、重命名等操作都会失败）
- 在此阶段Namenode收集各个datanode的报告，当书记块达到最小副本数以上时，会被认为是"安全"的，在一定比例（可设置）的数据块被确定为"安全"后，再过若干时间，安全模式结束。
- 当检测到副本数不足的数据块是，该快会被复制到达到最小副本数，系统中数据块大的位置并不是由namenode维护的，而是以块列表形式存储在datanode中。

### Block的副本防置策略
- 第一个副本：防置在上传的DN；如果是集群外提交，则随机挑选一台磁盘不是太满，CPU不太忙的节点；
- 第二个副本：防置在于第一个副本不同的机架（Rack）的节点上。
- 第三个副本：与第二个副本相同机架的节点。
- 更多副本：随机节点。

### HDFS写流程

图

流水线

距离优先

### HDFS读流程

### HDFS文件权限 POSIX
- 与Linux文件权限类似
  - r:read; w:write; x:execute
  - 权限x对于文件忽略，对于文件夹表示十分允许访问其内容

- 如果Linux系统用户A使用hadoop命令创建一个文件，那么这个文件在HDFS中的owner就是A；

- HDFS的权限目的：组织好人做错事，而不是阻止坏人做坏事。HDFS
相信，你告诉我你是谁，我就认为你是谁。

## Hadoop2

### Hadoop2产生背景
Hadoop1中HDFS和MapReduce在高可用、扩展性等方面存在问题

- NameNode单点故障，难以应用于在线场景， HA
- NameNode压力过大，且内存首先，影响扩展性， F
MapReduce存在的问题：
- JobTracker访问压力大，影响系统扩展性
- 难以支持出MapReduce之外的计算框架，比如spark、Storm等

Hadoop2.x由HDFS、MapReduce和YARN三个分支构成。
- HDFS： NN Federation（联邦）、HA；2.x只支持2个节点HA
，3.0实现一主多从；
- MapReduce： 运行在YARN上的MR；离线计算，基于磁盘I/O计算
- YARN：资源管理系统

### HDFS2
解决HDFS1中单点故障和内存受限的问题

解决单点故障：
- HDFS HA ：通过贮备NameNode解决
- 如果中NameNode发生故障，则切换到备NameNode上；

解决内存受限问题
- HDFS Federation 联邦
- 水平扩展，支持多个NameNode；
  - 每个NameNode分管一部分目录；
  - 所有NameNode共享所有DataNode存储资源；
HDFS2仅是架构上发生了变化，使用方式不变，对HDFS使用者透明，HDFS1中的命令和API仍可以使用。

图

Linux NFS
JournalNodes
最终一致性