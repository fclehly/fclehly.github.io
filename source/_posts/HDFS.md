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

DataNode和NameNode保持心跳，提交block列表；

HdfsClient和NameNode交互元数据信息；

HdfsClient和DataNode交互文件Block信息。

HdfsClient与NameNode交互后，向DataNode交互，NameNode起到了一定的负载均衡作用。