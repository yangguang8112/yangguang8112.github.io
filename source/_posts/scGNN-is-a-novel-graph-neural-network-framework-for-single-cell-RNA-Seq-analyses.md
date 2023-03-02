---
title: >-
  scGNN is a novel graph neural network framework for single-cell RNA-Seq
  analyses
date: 2022-02-21 14:34:05
tags: 文献阅读
---
![20220214-001909.png](https://tianchi-public.oss-cn-hangzhou.aliyuncs.com/public/files/forum/167776903484921341677769034376.png)

<!--more-->
### 主要分为三个部分

- LTMG预处理单细胞表达矩阵“转换”信号；
- 一个以细胞聚类任务收敛为目的的迭代过程，包括三个部分。1.Feature autoencoder，两个输入：来自第一步的正则化矩阵；初始化迭代来自预处理过的表达矩阵，之后输入就来自迭代最后一部分重构的表达矩阵。2.使用上一步自编码器得到的细胞的embedding构建cell graph然后作为Graph autoencoder的输入，然后使用graph embedding做细胞聚类。3.Cluster autoencoder，重构基因表达值，每个细胞类型中的细胞都有一个单独的cluster自编码器。
- Imputation autoencoder

## 实验设计
两个任务+解决一个科学问题

### imputation
四个带细胞类型标注的标准数据集：Chung, Kolodziejczy, Klein, and Zeisel
随机翻转非零表达值为零，模拟dropout
和9个imputation软件比较，评估方法有Median L1 distance, cosine similarity, 和 root-mean-square-deviation (RMSE) scores 这些指标均是在imputation后的数据和原始数据之间做。
除此之外还比较了DEGs的fold change的变化

### cell cluster
数据集同上。评估方法有10种，正文里显示的结果里是adjusted Rand index (ARI)和Silhouette
用了多种可视化方法，UMAP效果最好。
另外还用Klein’s timeseries data做了时序上的比较，分别用raw data和经过scGNN的imputation的数据通过monocle做伪时序分析。
还提了一下，从scGNN种的cell-cell graph中提取细胞通讯比随机的好（我估计效果好不到哪去，全文没有结果图展示，也没和cci的软件比较）

### 老年痴呆神经发育相关研究

#### 数据
6个AD+6个对照脑细胞共13214个单细胞（GSE138852）

#### 方法

1. 用scGNN做聚类，然后根据AD和对照的label可以得出哪些细胞在AD中比例多一点；
2. 合并六个oligodendrocyte子类与其他细胞类型（总共五类细胞）一起找差异基因（DEGs），其中又用raw data和scGNN处理后的数据做了一个对比，scGNN的差异基因聚类图信号更清晰；
3. AD对比对照组做通路富集，在五类细胞上聚类，可以看到前五个正调控的都是AD相关的通路。另外还发现一个很低的通路在microglia细胞上；
4. 使用一个做cell-type-specific regulon inference的工具IRIS3在这五类细胞上找到21个CTSR(cell-type-specific regulons)，发现了几个被报道过的AD相关的转录因子和目标基因。SP2和SP3，后面应该又去查了相关文献。

#### 结论
就是上面方法中得到的一些结论“这些发现为SP3在AD中的功能发现提供了一个方向”

## 除此之外还做了一些消融实验

## 总结

### 优点

1. 综合多方面特征。原始表达量、离散化表达量、细胞间关系，训练迭代中使用上一次迭代得到的聚类特征
2. 无监督

### 缺点

1. 每次训练只针对这一批数据，每次都需要长时间EM迭代训练
2. 超参数很多，不好调参
3. 代码优化不好，几乎不用GPU，很慢

### 改进

1. 将scGNN改为自监督的预训练模型；
2. 在预训练模型基础上加上scGNN的多种特征；
