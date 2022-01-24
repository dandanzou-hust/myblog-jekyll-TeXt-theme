---
# 【文献阅读】【技术分享】【竞赛】
title:  "【机器学习】斯坦福21秋季：实用机器学习（李沐版本）"
date:   2022-01-20 12:00:00 +0800
key: match-learning
categories: 机器学习
tag: "match learning
---
本课程是斯坦福大学2021年秋季新开的课程：实用机器学习。

作者李沐B站链接：[跟李沐学AI的个人空间_哔哩哔哩_bilibili](https://space.bilibili.com/1567748478/channel/collectiondetail?sid=28144)

## 1.1 课程介绍

机器学习工作流程：
$$
问题表述 \rightarrow 收集与处理数据 \rightarrow 训练与调优模型 \rightarrow 应用模型 \\
 \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \ \  \ \ \ \ \ \ \ \ \uparrow \ \ \leftarrow \  \ \ \ \ \ \ \ 监测 \ \ \ \ \ \ \ \  \leftarrow \ \ \ \ \ \ \ \ \downarrow
$$

挑战：

- 表述问题：聚焦最有影响的工业问题转变为机器学习问题，如无人驾驶、无人超市。
- 数据：高质量数据，隐私问题。
- 训练模型：模型越来越复杂，数据越来越大。
- 应用模型：大量计算不适合实时场景。
- 监测：数据分布变化、公平问题。

规则：

- 领域专家：有业务经验，知道数据重要性、从哪里获取、找到有价值的地方。
- 数据科学家：数据挖掘、模型训练、上线。
- 机器学习专家：定制化机器学习模型。
- 软件开发工程师 (SDE)：开发、维护代码，模型训练，管理资源，模型部署。

技能提升：
$$
软件开发工程师 \ \ \ \ \ \ 领域专家 \\
\ \ \ \ \ \ \  \downarrow \ \ \ \ \ \ \ \ \ \ \ \ \downarrow \\
\ \ \ \ \ \ \ 数据科学家 \\
\ \ \ \ \ \ \ \downarrow \\
\ \ \ \ \ \ \ 机器学习专家
$$
我理解的路线，先从开发出发，逐渐熟悉领域，转变为数据科学家。

## 1.2 数据获取

- 找数据

  -  常用数据集：MNIST、ImageNet、AudioSet、Kinetics、KITTI、Amazon Review、SQuAD、LibriSpeech

  - 常用途径：

    - Paperswithcodes Datasets: 提供了学术论文在数据集上的得分
    - Kaggle Datasets: 
    - Google Dataset search
    - 开源框架自带了部分数据集 tensorflow、huggingface。
    - 不同竞赛的数据集
    - Open Data on AWS:
    - Data lakes: 公司自己放数据的地方。

    学术数据集：干净、难度适中；选择受限、太简单、规模小；

    竞赛数据集：接近机器学习应用；还是简单、仅关注火热的话题；

    原始数据集：灵活；需要大量精力预处理；

  - 数据融合：多模态（不同来源的数据）融合

- 生成数据

  - GAN
  - 图像翻转、文本增强

## 1.3 网页数据抓取























