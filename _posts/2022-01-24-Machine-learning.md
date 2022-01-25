---
# 【文献阅读】【技术分享】【竞赛】
title:  "【机器学习】斯坦福21秋季：实用机器学习（李沐版本）"
date:   2022-01-20 12:00:00 +0800
key: machine-learning
categories: 机器学习
tag: "machine learning"
---
本课程是斯坦福大学2021年秋季新开的课程：实用机器学习。

作者李沐B站链接：[跟李沐学AI的个人空间_哔哩哔哩_bilibili](https://space.bilibili.com/1567748478/channel/collectiondetail?sid=28144)

## 1.1 课程介绍

机器学习工作流程：

![image-20220125103355933](https://gitee.com/ddzou/my-picture-bed-on-gitee/raw/master//markdown-picture//image-20220125103355933.png)

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

![image-20220125103429313](https://gitee.com/ddzou/my-picture-bed-on-gitee/raw/master//markdown-picture//image-20220125103429313.png)
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

- python selenium
- 云服务器可获取多个ip
- 注意法律风险

## 1.4 数据标注

- 半监督学习
- 众包
- 弱监督学习

半监督学习

​	自学习：

​		有标签数据 -> 训练模型 -> 预测无标签的数据  -> 数据合并   循环处理             

主动学习

​	模型筛选出难以确定的图片、人工标注

弱监督学习

​	设定多个规则，符合一定规则且数量超过阈值标为正。

## 2.1 探索性数据分析

技巧：

seaborn是matplotlib更高一层封装，有更多功能

pandas可以读取 zip 文件

```python
# 打印图片更高清
from IPython import display
display.set_matplotlib_formats('svg')
```

流程：

去空、缺值，转换数据类型、查看去除异常值。

## 2.2 数据清洗

错误数据：范围外、空值、类型错误

用各种数据分析工具，可视化数据分布，人为制定规则、模式来清洗数据。

## 2.3 数据转换



