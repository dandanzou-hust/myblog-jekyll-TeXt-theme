---
title:  "【竞赛】Root Cause Analysis for Wireless Network Fault Localization"
date:   2021-12-15 12:00:00 +0800
key: Root-Cause-Analysis-for-Wireless-Network-Fault-Localization
categories: 竞赛
tag: "RCA"

# 左侧菜单栏
# sidebar:
#  nav: "docs"
toc: true # 右侧导航栏
# read_time: true
# comments: true
# share: false
# related: true
---
[比赛链接](https://www.aiops.sribd.cn/home/statement)

## 赛题分析
[赛题分析](https://mp.weixin.qq.com/s/7FjAJq81PaA_XZgWm91aNw)

## 数据和评价指标
> 数据描述  

本数据集包括：  
* 变量因果关系图。在此数据集中，我们提供了一张专家绘制的变量因果关系图（已脱敏处理）作为先验。
* 变量特征数据。本数据集共包含2984个样本，每个样本为采自不同的5G路测场景的时间片段（长度不固定），其包含23个可观测特征变量（图1无色椭圆形）在该时间片段内随时间变化的信息。在这2984个样本中，只有少数数据（45%）是标注根因的已知异常数据，其余数据未知。
## 提交方式和记录
测试时，给出多个时间段数据，要求选手给出每个时段的根因及其概率，并设定阈值给出相应的根因集合。

选手需提交如下的csv格式文件，选手需将其认为的根因标记为1。

|数据段编号|	根因1|根因2|根因3|根因4|根因5|根因6|
|--------|------|-----|----|----|--|---|
|时间片段1|	0|	1|	1|	0|	0|	0|
|时间片段2|	0|	1|	1|	1|	0|	0|
| …… | …… | …… | …… | …… | …… |	…… |

## 技术方案
- 因果推理
DoWhy+EconML 可以用于决策因果关系，推理出包含根因概率的有向无环图。
- 用时序异常检测算法实现

