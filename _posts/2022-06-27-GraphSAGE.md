---
title:  "【文献阅读】GraphSAGE"
date:   2022-6-27 12:00:00 +0800
key: GraphSAGE
categories: 文献阅读
tag: "GNN"
---

# GraphSAGE

参考文献：[Hamilton W, Ying Z, Leskovec J. Inductive representation learning on large graphs[J]. Advances in neural information processing systems, 2017, 30.](https://zdd-1300938198.cos.ap-beijing.myqcloud.com//my-picture-bed/GraphSAGE.pdf)

## 引言

近期在看GNN相关内容，本文提出了GraphSAGE模型，已在Pytorch Graph（PyG包）中构建。

## Abstract

需求：图的节点embedding

问题：需要全图节点信息

方案：本模型局部邻域采样，聚合特征

效果：高于baseline

## Introduction

较大的Graph中，节点嵌入为低维向量，可以做后续的节点分类、聚类、链接预测。但是会遇到如图是动态变化的，遇到不可见的节点等问题

GNN：矩阵分解

GCN：构造卷积核

### GraphSAGE

功能：Node embedding

特性：适用不可见节点，学习拓扑和邻域节点特征，适用无节点特征的图。

方案：不是每个节点单独训练嵌入向量，而是训练一组聚合器函数。

适用：无监督、监督

效果：三个baseline都有提升。使用了两个基于引文数据和Reddit post数据的文档图（分别预测论文和帖子类别），以及一个基于蛋白质-蛋白质相互作用数据集的多图综合实验（预测蛋白质功能）。

## Method

核心思路：学习如何从节点的局部邻域（例如，附近节点的度或文本属性）聚合特征信息。

### 嵌入生成（即前向传播）算法

几个基本符号：

- K聚合函数的参数：$AGGREGATEk, \forall k \in \{1，…,k\}$

- 聚合的权重矩阵，传输信息：$W^K, \forall k \in \{1，…,k\}$

该算法直觉：在每次迭代（或搜索深度）时，节点都会聚集来自其本地邻居的信息，并且**随着该过程的迭代，节点会逐渐从图的更远处获得越来越多的信息**。

<img src="https://zdd-1300938198.cos.ap-beijing.myqcloud.com//my-picture-bed/20220627152704.png"/>

备注：K=2和S1*S2<500可取得高性能表现。

### 学习GraphSAGE参数

为了无监督学习来预测表示，基于图的损失函数鼓励**附近的节点具有类似的表示**，同时**强制要求不同节点的表示具有高度不同性**：

<img src="https://zdd-1300938198.cos.ap-beijing.myqcloud.com//my-picture-bed/20220627154710.png"/>

$v$是固定步长随机游走节点$u$的相邻节点，$\sigma$是sigmoid函数。$P_n$是负采样分布，$Q$定义了负样本数量。

**$z_u$是由节点邻居生成的，而不是像其他函数一样逐节点训练embedding**。上式可以被替换，如cross-entropy loss。

## 聚合结构

节点邻居是无序的，因此聚合函数不能受输入顺序的影响。以下有三种聚合函数：

**Mean aggregator：取向量元素平均值**

<img src="https://zdd-1300938198.cos.ap-beijing.myqcloud.com//my-picture-bed/20220627160517.png"/>

上式类似于空间卷积操作，区别是我们不连接之前的表示层$h^{k-1}_v$，跳连接，可极大增加性能。

**LSTM aggregator：LSTMs更加强大表达能力**

邻居随机排序，使其适用于语序集合。

> 我对此效果存疑

Pooling aggregator：池化聚合器，对称，可训练

每个邻居向量适用全连接层传送，elementwise max-pooling操作聚合邻居信息

<img src="https://zdd-1300938198.cos.ap-beijing.myqcloud.com//my-picture-bed/20220627161322.png"/>

备注：最大池化和平均池化效果差不多。

## Experiments

3个benchmark：

（i）使用科学网引文数据集将学术论文分类为不同的主题

（ii）将Reddit帖子分类为属于不同的社区

（iii）对各种生物蛋白质-蛋白质相互作用（PPI）图中的蛋白质功能进行分类。

<img src="https://zdd-1300938198.cos.ap-beijing.myqcloud.com//my-picture-bed/20220627161550.png"/>

## Conclusion

提出了GraphSAGE，可为不可见节点嵌入，效果好，节点邻域采样，平衡了性能和运行效率。

潜在的应用包括：有向图、多模态图、非均匀领域采样

# 代码尝试

该文章中算法已在PytorchGraph包中落地，可直接调用。
