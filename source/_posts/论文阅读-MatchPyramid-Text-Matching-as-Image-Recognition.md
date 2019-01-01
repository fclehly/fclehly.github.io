---
title: '论文阅读: MatchPyramid: Text Matching as Image Recognition'
date: 2018-05-29 21:07:40
tags:
- NLP
- NLM
- Deep Learning
categories:
- Paper
thumbnail: "http://p5.qhimg.com/t01e48ab37b3bf64f4a.png"
---
#<script type="text/javascript" src="http://cdn.mathjax.org/mathjax/latest/MathJax.js?config=default"></script>

## 论文信息
题目： Text Matching as Image Recognition  
作者： Liang Pang, Yanyan Lan, Jiafeng Guo, Jun Xu, Shengxian Wan, and Xueqi Cheng
会议： AAAI2016
## Abstract
匹配两个文本是许多自然语言处理任务中的一个基本问题。 一种有效的方法是从单词，短语和句子中提取有意义的匹配模式以产生匹配的分数。 受卷积神经网络在图像识别中的成功启发，神经元可以基于提取出的基本视觉模式（如定向边和角）捕捉许多复杂图案，我们建议将文本匹配建模为图像识别问题。 首先，构造一个匹配矩阵，其条目表示词语之间的相似性，并将其视为图像。 然后利用卷积神经网络以逐层方式捕获丰富的匹配模式。 实验表明，通过类似图像识别模式的组成层次，我们的模型可以成功识别显着信号，例如n-gram和n-term匹配。 实验结果证明了它相对于baseline的优越性。
## Introduction
问题定义如下：
$$Match(\boldsymbol{T_1}, \boldsymbol{T_2})=F(\Phi(\boldsymbol{T_1}), \Phi(\boldsymbol{T_2}))$$
\\(T_1 = (w_1, w_2, ..., w_m)\\) 和 \\(T_2 = (v_1, v_2, ..., v_n) \\)是两个文本，\\(\Phi \\)是一个将文本映射到向量的函数。
本文贡献：   
1. 一个关于使用图像识别做文本匹配的新的视角；
2. 一个新的深度网络结构MatchPyramid，基于匹配矩阵（它能在词，短语到整个句子这些不同层次上提取信息）；
3. 在不同任务上展现了这个结构强大的能力；

## Motivation
![motivation](motivation.png)
作者认为两个句子的匹配和图像识别任务有很多的相似处，比如上图中的两个句子词、短语、句子层面的匹配可以类比到图像识别中的边、角、模式、部分等层次的特征提取，如下图。
![relationship](relationship.png)

## 网络简介
网络结构如下所示：

![模型预览](model_overview.png)

### 匹配矩阵
这个匹配矩阵是这篇paper的关键所在，它将句子语义匹配与图像识别联系了起来。

paper中介绍了三种构建匹配矩阵的方法：
1. **Indicator**: 
就是判断两个句子中的词是否分别相同
![](indicator.png)
2. **Cosine**:
两个句子进行词嵌入编码后，每个词向量分别计算余弦值；
![](cosine.png)
3. **Dot Product**: 
两个句子进行词嵌入编码后，每个词向量分别点积即可，其实就是一个句子表征矩阵乘以另一个句子表征矩阵的转置；
![](dot_product.png)

例子如下：
![](indicator_and_dot_product.png)

### 其他一些细节
因为句子长度可能不一致，所以每次生成的匹配矩阵的size就可能不一样，因此作者采用了Hierarchical Convolution解决这个问题。
![](hierarchical_convolution.png)
* 第一层卷积的pooling使用dynamic pooling生成一个一定size的feature map；
* 第二层的卷积求和算。   
* 激活函数是ReLU；
* 2层MLP，softmax输出预测结果；
