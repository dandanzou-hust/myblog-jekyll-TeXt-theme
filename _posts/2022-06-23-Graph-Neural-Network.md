---
title:  "【技术分享】GNN综述"
date:   2022-6-23 12:00:00 +0800
key: Graph-Neural-Networks
categories: 文献阅读
tag: "GNN"
---

# 2020年GNN综述

Zhou J, Cui G, Hu S, et al. Graph neural networks: A review of methods and applications[J]. AI Open, 2020, 1: 57-81.

适用任务：

1. 节点分类：用户分类

2. 链路预测：节点是否可能存在链接

3. 聚类：欺诈用户风险识别

## pipeline

1. 找到图结构
   
   一般分两种：
   
   - 显式：分子、知识图谱
   
   - 隐式：文本，图像。需构造图结构，再基于构造的图设计GNN模型。

2. 定义图形类别和规模
   
   类型可自由组合：
   
   - 有向/无向图：边是否有方向
   
   - 同质/异质图：节点和边有不同的类型
   
   - 静态/动态图：动态图应考虑时间信息
   
   规模：
   
   - 超过服务器计算上限的就认为是高了

3. 设计损失函数
   
   - 节点级任务：
     
     - 节点分类：尝试将节点分类为几个类，
     
     - 节点回归：预测每个节点的连续值。
     
     - 节点聚类：的目的是将节点划分为几个不相交的组，其中相似的节点应该位于同一组中。
   
   - 边缘级任务：
     
     - 边缘分类：边缘类型进行分类
     
     - 链接预测，预测两个给定节点之间是否存在边缘。
   
   - 图级任务：
     
     - 图分类
     
     - 图回归
     
     - 图匹配
     
     从监督的角度：监督、半监督、无监督（大部分任务都是半监督的）

4. 用计算模块构建模型
   
   通用的计算模块：
- 传播模块：用于在节点之间传播信息，以便聚合的信息可以捕获特征信息和拓扑信息。在传播模块中，卷积算子和递归算子通常用于聚合邻居的信息，而跳过连接操作用于从节点的历史表示中收集信息，并缓解过度平滑问题。

- 采样模块：当图较大时，通常需要采样模块来对图进行传播。采样模块通常与传播模块相结合。

- 池模块：当我们需要高级子图或图的表示时，需要池模块来从节点提取信息。

<img src="https://zdd-1300938198.cos.ap-beijing.myqcloud.com//my-picture-bed/2022-06-23-17-42-17-image.png"/>

## 计算模块

### 传播模块——卷积算子

频域和空间域

<img title="" src="https://pic1.zhimg.com/v2-dccb0b21bf0f21a55dca7ab5970e9eb4_r.jpg" alt="" width="388">

# 2022智源大会

## 图表征学习： 孙怡舟 加州洛杉矶分校

早期embedding: DeepWalk、LINE、Node2Vec等方法参数不共享太多了，无法添加新节点，

GNN挑战: 异质图、label缺乏、可测量性

方案：HGT、GPT-GNN、LADIES、GLNN

## 频域解释：魏哲巍 中国人民大学

![](D:/work/PersonalWeb/myblog-jekyll-TeXt-theme/assets/post-images/2022-06-23-15-43-40-image.png)

图卷积神经网络作为了一个滤波器，对当前节点的邻居节点信息进行过滤。

优化：BernNet、ChebNetII 两种

## 基于聚类的图对比学习研究：张静 中国人民大学

## CogDL： 清华

# GNN+通信网络综述

Jiang W. Graph-based deep learning for communication networks: A survey[J]. Computer Communications, 2021.

## Abstract

通信网络是当代社会的重要基础设施。基于图的深度学习在通信网络中的一系列问题上取得了最先进的性能。

综述内容：在不同类型的通信网络（如无线网络、有线网络和软件设计网络）的各种问题中使用基于不同图的深度学习模型（如图卷积网络和图注意力网络）的快速增长的研究主体。

<img src="https://zdd-1300938198.cos.ap-beijing.myqcloud.com//my-picture-bed/20220705101533.png"/>

## Graph-based Deep Learning Introduction

<img src="https://zdd-1300938198.cos.ap-beijing.myqcloud.com//my-picture-bed/20220705105240.png"/>

## 有线网络中routing

[85] F. Geyer, G. Carle, Learning and generating distributed routing protocols usinggraph-based deep learning, in: Proceedings of the 2018 Workshop on Big DataAnalytics and Machine Learning for Data Communication Networks, 2018, pp.40–45.

分布式routing的方法，Graph-Query Neural Network

[87] S. Xiao, H. Mao, B. Wu, W. Liu, F. Li, Neural packet routing, in: Proceedingsof the Workshop on Network Meets AI & ML, 2020, pp. 28–34.

华为做的，在用NN做负载均衡的基础上，加了优化的最短路径路由算法。
