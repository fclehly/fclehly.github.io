---
title: '论文阅读: MatchPyramid: Text Matching as Image Recognition'
date: 2018-05-29 21:07:40
tags:
- NLP
- NLM
- Deep Learning
categories:
- Paper
- Deep Learning
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
1. 一个关于使用图像识别做文本匹配的新的视角
2. 一个新的深度网络结构MatchPyramid，基于匹配矩阵（它能在词，短语到整个句子这些不同层次上提取信息）
3. 在不同
