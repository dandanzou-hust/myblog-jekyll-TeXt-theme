---
title:  "【文献阅读】Fast map matching, an algorithm integrating hidden Markov
model with precomputation"
date:   2021-12-17 12:00:00 +0800

categories: 文献阅读
tag: "Map Matching"
---
Fast map matching 文献个人翻译，若有疑问，请以原文为准。

## 前言

最近我在看地图匹配相关的论文和资料。其中，这篇文章有开源的算法包，使用起来比较方便，所以详细阅读了这篇文章。

## 摘要

**背景：**

全球定位系统（GPS）传感器的广泛部署产生了大量数据，在交通研究中有着广泛的应用。由于观测误差，通常会执行地图匹配（MM）过程，以从嘈杂的GPS轨迹推断道路网络上的路径。不断增长的数据量要求设计高效、可扩展的MM算法。

**工作：**

本文提出了一种结合隐马尔可夫模型和预计算的快速地图匹配（FMM）算法，并提供了一个开源实现。预先计算上界起点-终点表，以存储道路网络中特定长度内的所有最短路径对。被称为MM瓶颈的重复路由查询被`<u>`**哈希表搜索**`</u>`所取代。此外，在FMM中识别并解决了几个**`<u>`退化情况和反向运动`</u>`**问题。

实验：
对大量真实世界出租车出行轨迹的实验表明，FMM已经实现了相当大的单处理器MM速度，速度为25000–45000点/秒，随输出模式的变化而变化。对FMM中不同步骤的运行时间的研究表明，在采用预计算后，新的瓶颈位于候选搜索中，更具体地说，是GPS点到道路边缘多段线的投影。通过施加惩罚，结果中的反向移动也有效减少。

## 1. 介绍

得益于无线通信和定位技术的快速发展，全球定位系统（GPS）传感器已广泛部署在车辆、智能手机和自行车上，以收集运动信息。与交通研究中使用的传统固定传感器（如安装在道路网固定位置的环路检测器）相比，GPS传感器被认为是一种有前途的数据收集方法，具有广泛的空间覆盖范围，具有成本效益。它通常包含被跟踪对象的ID、纬度、经度和记录时间戳，而一些高级传感器也包含速度、航向和加速度。由于不可避免的测量误差和低采样率造成的不确定性（Li等人，2013年），道路网络上的连续运动被观察为一连串周期性报告的GPS观测数据，与真实位置有一定的偏移。为了推断物体穿越的路径，经常进行地图匹配（MM）过程，这是许多GPS数据相关研究的重要预处理步骤，如路线导航（Yuan等人，2010a）、旅行时间估计（Rahmani等人，2015）、交通建模（Castro等人，2012）和活动 识别（Liao等人，2007）。

在文献中，已经提出了许多算法来解决MM问题。在 Quddus等人（2007），早期的MM算法被分为四类，包括 几何学、拓扑学、概率学和其他高级算法。它们中的大多数都对 GPS误差、采样率和最初的MM结果（Newson和Krumm，2009；Zeng等人，2015）。2015). 由于隐马尔科夫模型（HMM）首次被用于解决MM问题，在 Newson和Krumm(2009)以及Lou等人(2009)首次采用隐马尔可夫模型(HMM)解决MM问题以来，它在后来的研究中被广泛采用。隐马尔科夫模型（HMM）在Newson和Krumm（2009）以及Lou等人（2009）的研究中首次被采用，因为它能优雅地、可扩展地整合GPS误差、几何学、拓扑学和其他因素。拓扑结构和其他因素在路径推断中的优雅和可扩展性。沿着这个方向，大部分的 的努力都是为了通过引入新的信息来提高MM的准确性。如限速（Yuan等人，2010b）、转弯角度（Li等人，2013）和弯曲度 (Zeng等人，2015)，或者为路径推断设计更稳健和现实的目标函数推断（Yuan等人，2010b；Wei等人，2012；Chen等人，2014）。然而，MM的性能 在这些研究中，MM的性能改进并没有得到充分的利用（Marchal等人，2005，Huang 等人，2013）。

考虑到GPS数据的规模越来越大，有必要开发能够在短时间内处理大量数据的MM算法。本文主要集中在性能的提高上，提出了快速地图匹配（FMM），一种将HMM与预计算相结合的算法。该算法包含两个阶段，在第一个预计算阶段，从路网中创建了一个有上界的原点目的地表，以存储所有对的信息。在第一个预计算阶段，从路网中创建了一个有上界的原点-目的地表，以存储所有长度在一定阈值之下的最短路径对的信息。在第二阶段，进行MM计算，其中计算成本高的路由查询被替换为散列查询。昂贵的路由查询被替换为哈希表搜索。本文的贡献 这篇文章的贡献强调如下。

(1) 开发了一种高效、稳健的MM算法，并提供了开源实现。本文发现并解决了MM中的几个退化案例。在以前的算法中发现了一个反向运动的问题，并通过应用惩罚来解决。

(2) 用一个大型的GPS轨迹数据集评估了该算法的各个方面。结果表明，FMM已经达到了相当高的单处理器MM速度，即25,000-45,000点/秒，随输出模式的不同而不同。对FMM中各步骤运行时间的调查显示，在采用预计算后，瓶颈已从先前已知的重复路由查询转移到候选搜索（CS）。更确切地说，是GPS点在道路边缘多线上的投影。通过施加惩罚，结果中的反向移动被有效减少。

本文的其余部分组织如下。第2节提供了一个系统的文献综述，第3节给出了前言。第4节中介绍了该算法。在第5节中，报告了一个关于真实世界的GPS数据集的案例研究。最后，在第6节中得出结论，并对未来的工作进行了规划。

## 2. 相关工作

在文献中，已经提出了几种方法来提高MM的性能。MM的性能，这些方法可以分为四类：空间索引、轨迹简化。并行计算，以及先进的路由算法。

空间索引技术，如R-树guttman 1984和四叉树（guttman 1984）以及四叉树（Finkel和Bentley 1974）已广泛应用于MM算法中，以加速GPS点周围相邻边的搜索（Marchal et al.2005，Li et al.2011，Wei et al.2012，Chen et al.2014）。然而，空间索引的贡献仅限于候选搜索，并且通常与其他方法协作使用。

轨迹简化方法通过减少要匹配的点的数量来提高性能，这对于密集和重复观测尤其有效。Li等人（2014）提出了三种简化算法：增量、切片和全局窗口，通过基于轨迹的几何信息为GPS点分配权重，并对权重较小的点进行过滤。在作者最近的工作（Li et al.2015）中，提出了一种基于移动方向的定向包围矩形算法来对轨迹中的点进行聚类，该算法还能够处理异常值。然而，这些算法仅限于密集的GPS数据。

并行计算也是高性能MM的一项很有前途的技术，因为不仅可以对单个轨迹进行匹配，而且还可以对轨迹内每个GPS点的候选边进行并行搜索。随着分布式和并行计算技术的快速发展，在以前的研究中已经报告了相当大的改进。Li等人（2011年）设计了在线MM框架，并在16节点集群上进行了测试，总体性能约为20000点/秒。类似地，在Huang等人（2013）中，20节点集群的最高速度为120000点/秒。然而，这些研究并没有充分利用单处理器性能的潜在改善。

与上述三类相比，高级路由算法更受关注，因为基于HMM的MM算法的瓶颈已被广泛认为是重复路由查询（Lou等人2009、Wei等人2012、Rahmani和Koutsopoulos 2013、Zhe和Zhang 2015）。除了经典的Dijkstra算法（Dijkstra 1959），还提出了各种高级路由算法来加速最短路径查询，如A*（Hart等人1968）和ALT（Goldberg和Harrelson 2005）。然而，它们对MM的贡献是有限的，因为它们主要集中于查询一个较大的图形，其中起点和终点距离较远，而在MM上下文中，连续GPS观测之间的距离可能较小。在Rahmani和Koutsopoulos（2013）中，通过根据GPS观测之间的时间间隔估计的距离构建局部搜索树，避免了遍历整个网络图。Chen等人（2014年）取得了更显著的改进，通过在网络图中添加虚拟节点和连接GPS点与相邻边的链路，减少了重复查询的数量。因此，将多个源到多个目标的路由查询转换为传递一系列虚拟节点的单个查询。但是，它经常更新每条轨迹的网络图，这会降低性能。解决该问题的另一种方法是预计算一些路由结果。在Zhe和Zhang（2015）中，从道路网中创建一个上界起点和终点表，并进行查询，以获得候选道路之间的最短路径距离。本文开发的FMM基于相同的原理提高了性能，但在几个方面与他们的工作有所不同。首先，完全解决了重复路由查询的瓶颈，实现了更高的性能。其次，报告了将预计算集成到MM中的各个方面，包括对配置的敏感性、运行时构成和远程路由查询的处理。第三，确定并解决了几个退化情况和反向运动问题。

## 3. 准备工作

道路网络表示为有向图：

$$
G = (V,E)
$$

其中V是节点集，E是边集。V中的每个节点都存储为一个点，表示道路段的交叉点和死角。每条边E表示ID为Eid的有向路段，从ID为s的源节点开始到ID为t的目标节点。e的几何图形是由长度为L的geom表示的多段线。
因此，边e存储为元组，表示为

$$
e = (eid, st, geom, L)
$$

e上的移动被限制在从节点s到t的方向上。为简洁起见，边e也被写为 (s, t) 在随后的内容中。

基于本文收集的GPS数据格式，GPS观测p记录特定时间戳ts处被跟踪对象的纬度lat和经度long，表示为元组

$$
p=(lat,long,ts)
$$

也称为GPS点。

GPS轨迹tr是一个由GPS点组成的时间序列，定义为：

$$
tr = <p1,p2,…,pN>
$$

存储为多段线。为了简单解释，在后面的内容中，跟踪对象被假定为车辆。

## 4. FMM algorithm

与大多数以前的工作类似，FMM采用GPS轨迹集TR和道路网G作为输入。对于每条轨迹，它导出一条路径，该路径存储为车辆穿过的一系列边及其几何体。图1显示了FMM的整个过程，包括预计算和MM两个阶段。

![image-20211220111258678](../assert/2021-12-17-Fast_map_matching,_an_algorithm_integrating_hidden_Markov_model_with_precomputation/image-20211220111258678.png)

### 4.1. Precomputing stage

在预计算阶段，计算长度低于阈值Δ的G中的所有最短路径对（SP），并以压缩方式存储在上界起点-终点表（UBODT）中。UBODT中的每一行对应于从源节点no到目的节点nd的SP，并由以下表达式表示：

$$
R(n_o,n_d)=(n_o,n_d,next_n,next_e,prev_n,dist)
$$

其中next n和next e分别表示no之后访问的下一个节点和下一条边。nd之前访问的上一个节点存储在prev n中，dist是SP距离。图2给出了存储在UBODT中的这些信息的图示。

![image-20211220112827018](../assert/2021-12-17-Fast_map_matching,_an_algorithm_integrating_hidden_Markov_model_with_precomputation/image-20211220112827018.png)

UBODT是通过迭代G中的每个节点s并调用经典的单源Dijkstra算法来构造的，该算法从s开始，上界为Δ，如算法1所示。在每次迭代中，生成最短路径树，如算法1中的第4-10行所示。

![image-20211220113158193](../assert/2021-12-17-Fast_map_matching,_an_algorithm_integrating_hidden_Markov_model_with_precomputation/image-20211220113158193.png)

构造后的UBODT存储为一个哈希表，其复合键为(n_o,n_d)，实现为一个线性链接结构。该结构由两个参数配置：

-the number of buckets **H**（通常选择为素数）

-Multiplier **M**（用于计算唯一键 n_o∗M+n_d，可选择其为G中的节点数）

给定一个道路R(n_o,n_d )，将其插入bucket中，索引为h：

$$
h=(n_o∗M+n_d ) mod H
$$

其中，0<=h<=H。UBODT中查询的性能与定义为lf = |UBODT|/H的加载因子密切相关，该加载因子表示哈希表的一个存储桶中存储的元素的平均数量。

从UBODT可以高效地查询SP信息。从节点no到nd的SP距离，用I^N (n_o,n_d)表示，如下所示：

$$
I^N (n_o,n_d )=R(n_o,n_d ).dist
$$

它递归地查询从no到nd访问的下一条边，直到到达目的地。
虽然从下一个n开始构建路径就足够了，但上一个n也存储在UBODT中，主要用于第4.3节后面解释的反向运动校正。如果在UBODT中未发现节点对(n_o,n_d) ，这意味着I^N (n_o,n_d) >Δ，可以调用传统的Dijkstra算法。

### 4.2 MM阶段

在MM阶段，HMM与预计算相结合，在考虑GPS误差和拓扑约束的情况下推断车辆经过的路径。该阶段分为四个步骤：候选搜索（CS）、最佳路径推理（OPI）、完整路径构造（CPC）和几何构造（GC）。

#### 4.2.1 候选搜索（CS）

此步骤搜索轨迹中每个点的候选边。候选边的示例如图3所示。当选择e作为GPS点p的候选边缘时，车辆的位置被估计为e.geom上p的投影点，用^p表示。点^p也称为p的候选点。候选C存储为元组

$$
C=(p ̂,e,dist,λ)
$$

其中dist是从p到^p的欧几里德距离，λ是沿e.geom从e.s到^p的偏移距离，0<λ<e.L。点在多段线上的投影通过线性参考算法求解，方法是迭代多段线上的每个线段并返回距离最小的线段。

给定GPS点p，候选集被构造为搜索半径r内p周围道路边缘的k近邻（KNN），表示为:

$$
CS(p)=KNN(p)_{(k,r)}
$$

在本文中，R-tree被用来加速CS。尽管Roussopoulos等人（1995）研究了使用R-树的KNN搜索，但由于R施加的额外约束，它没有用于FMM。在实践中，很可能会指定一个小R，相交查询将返回很少的边，从而可以有效地检索KNN。在FMM中，R-tree上的KNN查询分为以下三个步骤：

（1） R-tree 相交：创建一个中心为p、边长为2r的正方形，并在R-tree上执行相交查询，以导出其边界框与正方形相交的所有边。

（2） 候选构造：对于上一步中获得的每条边e，将p投影到e.geom上的^p。如果是dist<=r，为e创建一个候选对象。

（3） KNN排序：根据dist对上一步找到的所有候选项进行排序并返回k个最小的。

根据上述定义，给定轨迹tr，轨迹候选集（TRCS）：

$$
TRCS(tr)=<CS(p_1 ),CS(p_2),…,CS(p_N)>
$$

![image-20211223182127115](../assert/2021-12-17-Fast_map_matching,_an_algorithm_integrating_hidden_Markov_model_with_precomputation/image-20211223182127115.png)

#### 4.2.2 最佳路径推理（OPI)

在这一步中，首先基于HMM模型从候选轨迹构建一个过渡图，可以查询候选轨迹之间的SP距离。在此基础上，推导出轨迹的最优路径。下面给出隐马尔可夫模型的定义和OPI的详细描述。

可以直接从UBODT检索图的节点之间的SP距离。然而，计算从Cn到Cn+1的SP距离需要额外的处理，这实际上是图的边上的点之间的距离，而不是节点之间的距离。假设车辆始终沿边缘方向移动，图4显示了Pn和Pn+1的所有可能移动，如下所示：

![image-20211223182156634](../assert/2021-12-17-Fast_map_matching,_an_algorithm_integrating_hidden_Markov_model_with_precomputation/image-20211223182156634.png)

根据观察结果，从Cn到Cn+1的SP距离计算如下(详细推导请参见原文)：

![image-20211223182207002](../assert/2021-12-17-Fast_map_matching,_an_algorithm_integrating_hidden_Markov_model_with_precomputation/image-20211223182207002.png)

#### 4.2.3 最佳路径推理（OPI)

在文献中，MM的HMM定义主要有三部分不同：转移概率、发射概率和要最大化或最小化的目标函数。转移概率的一个常用定义是欧几里德距离除以SP距离，这在GPS数据密集和搜索半径较大的情况下可能会出现问题。

![image-20211223182315783](../assert/2021-12-17-Fast_map_matching,_an_algorithm_integrating_hidden_Markov_model_with_precomputation/image-20211223182315783.png)

在FMM中，优化后的转换概率定义为：

![image-20211223182300657](../assert/2021-12-17-Fast_map_matching,_an_algorithm_integrating_hidden_Markov_model_with_precomputation/image-20211223182300657.png)

发射概率，采用Lou等人（2009）使用的相同定义，假设GPS点在车辆真实位置周围遵循零平均高斯分布。形式上，候选Cn的发射概率定义如下：

![image-20211223182333394](../assert/2021-12-17-Fast_map_matching,_an_algorithm_integrating_hidden_Markov_model_with_precomputation/image-20211223182333394.png)

![image-20211223182337849](../assert/2021-12-17-Fast_map_matching,_an_algorithm_integrating_hidden_Markov_model_with_precomputation/image-20211223182337849.png)

采用Viterbi算法，可以有效地推断出最优路径O路径为具有最大分数的路径，该路径存储为tr中每个点匹配的N个候选序列，表示为

![image-20211223182343252](../assert/2021-12-17-Fast_map_matching,_an_algorithm_integrating_hidden_Markov_model_with_precomputation/image-20211223182343252.png)

#### 4.2.4 完整路径构造（CPC）和几何构造（GC）

一旦获得O路径，最后两个步骤是CPC和GC。在CPC步骤中，通过将连接O路径中连续候选的SP连接在一起，构建完整路径C路径，这是tr穿过的一系列边，表示为

![image-20211223182515741](../assert/2021-12-17-Fast_map_matching,_an_algorithm_integrating_hidden_Markov_model_with_precomputation/image-20211223182515741.png)

路径的查询也在UBODT中执行。

在以前的一些工作中，针对候选的每个过渡都查询和存储SP距离和路径，这浪费了计算资源，因为最终只导出其中一个。FMM仅通过在获得O路径后查询SP路径来解决该问题。

最后通过连接C路径中每条边的多段线来构造相应的几何图形。

#### 远程路由查询的处理

在FMM中，在OPI步骤中查询UBODT以获取SP距离，在CPC步骤中查询详细路径。实际上，连续候选人之间的SP距离可能大于Δ。因此，UBODT不会返回任何SP信息。为了解决该问题并评估通过预计算获得的改进，FMM的四种模式设计如下：

(M1) UBODT+Dijkstra (brute force):当在OPI步骤的UBODT中未找到SP距离或在CPC步骤中未找到SP路径时，第一种模式直接调用Dijkstra算法。

(M2) UBODT+Dijkstra (optimized):为了避免上述不必要的查询，如果在UBODT中找不到Δ，则第二种模式首先返回Δ作为SP距离的估计值。

(M3) UBODT: 如果在OPI步骤的UBODT中未找到Δ，则第三种模式也返回Δ作为距离。

(M4) Dijkstra: 在不查询UBODT的情况下，最后一种模式始终调用Dijkstra算法。

#### 反向移动惩罚

当使用HMM模型匹配真实世界的GPS数据时 ，结果中经常观察到车辆在对应于单个双向路段的两条定向边上来回移动，这称为反向移动。

![image-20211223182559452](../assert/2021-12-17-Fast_map_matching,_an_algorithm_integrating_hidden_Markov_model_with_precomputation/image-20211223182559452.png)

虽然可能会发生车辆在路段上实际来回移动的情况，但更常见的情况是存在双向路段且GPS观测误差较大。因此，包含反向移动的路径可以在OPI步骤中返回，因为它的分数高于正确的路径。

![image-20211223182610259](../assert/2021-12-17-Fast_map_matching,_an_algorithm_integrating_hidden_Markov_model_with_precomputation/image-20211223182610259.png)

SP距离添加反向移动的惩罚项：

![image-20211223182621483](../assert/2021-12-17-Fast_map_matching,_an_algorithm_integrating_hidden_Markov_model_with_precomputation/image-20211223182621483.png)

## 5. 示例

本案例研究中使用的出租车GPS数据最初是在Mobile Millennium项目中收集的。该数据集涵盖了从2013年3月1日到2013年3月31日的一个月。

连续GPS记录之间的时间间隔和距离统计如图所示，包含约1300万个GPS观测值，1500辆车辆。每个记录都以以下格式存储（记录ID、车辆ID、时间戳、纬度、经度、租用）。租用的最后一个属性是一个布尔值，指示出租车是否由消费者租用。

GPS数据稀疏，大多数时间间隔在1分钟到4分钟之间，大多数距离在2公里以下。从原始GPS数据中，出租车行程被提取为具有真实租用状态的连续记录序列，并存储为多段线。共提取了673849条轨迹，并根据以下准则对其进行预处理以去除异常值：

（1）轨迹仅包含一个点；

（2） 它的平均速度在150公里/小时以上

（3） 其几何图形超出了道路网络的边界。最后保留644695次行程（95.7%），包含6812720点。根据经验GPS误差设置为100米。

![image-20211223182657802](../assert/2021-12-17-Fast_map_matching,_an_algorithm_integrating_hidden_Markov_model_with_precomputation/image-20211223182657802.png)

![image-20211223182705637](../assert/2021-12-17-Fast_map_matching,_an_algorithm_integrating_hidden_Markov_model_with_precomputation/image-20211223182705637.png)

![image-20211223182712203](../assert/2021-12-17-Fast_map_matching,_an_algorithm_integrating_hidden_Markov_model_with_precomputation/image-20211223182712203.png)

![image-20211223182715974](../assert/2021-12-17-Fast_map_matching,_an_algorithm_integrating_hidden_Markov_model_with_precomputation/image-20211223182715974.png)

![image-20211223182721141](../assert/2021-12-17-Fast_map_matching,_an_algorithm_integrating_hidden_Markov_model_with_precomputation/image-20211223182721141.png)

## 6. 准确率和结果展示

![image-20211223182741765](../assert/2021-12-17-Fast_map_matching,_an_algorithm_integrating_hidden_Markov_model_with_precomputation/image-20211223182741765.png)

![image-20211223182747771](../assert/2021-12-17-Fast_map_matching,_an_algorithm_integrating_hidden_Markov_model_with_precomputation/image-20211223182747771.png)

![image-20211223182751852](../assert/2021-12-17-Fast_map_matching,_an_algorithm_integrating_hidden_Markov_model_with_precomputation/image-20211223182751852.png)

## 参考文献

[Fast map matching, an algorithm integrating hidden Markov model with precomputation.pdf](../assert/2021-12-17-Fast_map_matching,_an_algorithm_integrating_hidden_Markov_model_with_precomputation/Fast map matching, an algorithm integrating hidden Markov model with precomputation.pdf)
