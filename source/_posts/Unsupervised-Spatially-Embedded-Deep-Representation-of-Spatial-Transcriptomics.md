---
title: Unsupervised Spatially Embedded Deep Representation of Spatial Transcriptomics
date: 2022-03-22 14:21
tags: 文献阅读
---

## 目的
优化转录数据和空间信息的整合，和stlearn类似，stLearn用的形态学信息，stLearn虽然用了卷积提取了图片的语义信息，但是是用的一个通用的预训练模型然后在形态学图片上做的fine-tune
空间转录组的数据可以分为三部分：基因表达、空间信息、形态学信息

<!-- more -->
实验部分
- 在human dorsolateral prefrontal cortex data上获得了更好的聚类准确性；还做了轨迹分析
- batch整合
- 在human breast cancer data 上做了

列举了一些方法，BayesianSpace, Giotto, SpaGCN, stLearn，有一个共同特点是，最后都要接上PCA、UMAP和spatial clustering
这样导致模型只能表示线性关系， 虽然stLearn有用到深度学习，但是只在图像上处理了形态学数据，模型仍然以来PCA来提取归一化后的表达量特征
本文提出的方法除了解决这个问题，产生的结果天然就是联合了基因表达和空间信息的embedding表式

## 框架

- 介绍整个处理框架
- 和其他软件比较结果（包括聚类、轨迹分析、batch校正），分别用的软件是（Leiden, Monocle3, Harmony）
- 用SEDR分析肿瘤异质性和免疫微环境（这里应该是自己的数据）
- 最后说SEDR可以处理更高分辨率的空间转录组

![20210816-130209.png](https://tianchi-public.oss-cn-hangzhou.aliyuncs.com/public/files/forum/167803382488030421678033823622.png)



## Method 

### 数据预处理

Scanpy预处理归一化raw gene expression counts到library sizes，然后筛选掉一些异常高表达的基因，然后用PCA提取前200个pc生成初始的基因表达矩阵

### Graph construction for spatial transcriptomics data

用空间坐标计算细胞间的欧式距离，每个细胞选取最近的10个邻居构建一个邻接矩阵$A$是一个对称的，当$i$和$j$是邻居则$A_{ij}=A_{ji}=1$否则等于0

#### Deep autoencoder for latent representation learning

这个不需要过多的讲，就是一个两层的MLP

输入是基因表达矩阵$X \in \mathbb{R}^{N \times M}$，经过encoder之后得到每个细胞的低维向量表示$Z_f \in \mathbb{R}^{N \times D_f}$，然后把$Z_f$和$Z_g \in \mathbb{R}^{M \times D_g}$concatenate起来，得到$Z \in \mathbb{R}^{N \times D}$，即$D = D_f + D_g$，最后通过decoder得到还原的$X^{\prime} \in \mathbb{R}^{N \times M}$，最后loss函数是MSE均方误差$\sum (X - X^{\prime})^2$

#### Variational graph autoencoder for spatial embedding

#### Batch effect correction for spatial transcriptomics

使用的是unsupervised deep embedded clustering (DEC)方法。先用K-means算法从得到的隐变量$Z$中得到一个初始化的聚类中心就是公式中的$\mu_j$，类的个数是个超参数。然后就是迭代优化，分两步：第一步，使用一个t分布计算点$z_i$到中心$\mu_j$的软分配$q_{ij}$，这里t分布的自由度取1，所以公式中简化了。第二步，迭代优化clusters通过学习一个基于$q_{ij}$的辅助分布$p$的高置信分配。loss函数就是$q_{ij}$和$p_{ij}$之间的KL散度

#### Evaluation metrics for clustering

$$ ARI = \frac{RI - E[RI]}{max(RI) - E[RI]} $$

$$ RI = \frac{a + b}{C^2_n} $$

$RI$就是unadjusted rand index，预测的label set和真是的label set可以组成$C_n^2$个pairs，$a$表示来自相同集合的正确标注的pairs的数量，$b$表示不在相同集合的pairs的数量。$E[RI]$是random labeling的$RI$的期望。$ARI$越高表示聚类越好。




## 实验
为了定量评价SEDR和其他方法的好坏，使用human dorsolateral prefrontal cortex (DLPFC) data这个数据。这个数据有清晰的形态学边界，可以作为ground truth。使用标准的Seurat pipeline进行数据预处理和聚类作为baseline，它只用到了基因表达数据。然后再使用stLearn,SpaGCN,BayesSpace以及本文的SEDR
据Fig.2A可以看出SEDR表现第二，但是和表现第一的BayesSpace差距不大。并且，后者的聚类算法是被spatial omics优化的，而SEDR只是一个降维的方法可以找到最好的隐藏表示，后面接的是Leiden clustering，没有专门为spatial omics优化的。而且BayesSpace不会产生隐变量用于后续分析。

### 肿瘤微环境
fig4A
SEDR聚类更平滑，其他方法边界粗糙。SEDR还能在肿瘤区域发现更多的子类，其他方法倾向于把健康区域划分子类。
在肿瘤区域DCIS/LCIS_3中SEDR分离出一个tumor core，图上显示是cluster 3
fig4B
每个spot得到一个分类可能性，这个可能性可以描述免疫细胞或者成纤维细胞所在的位置
fig4C
为了进一步描述DCIS/LCIS_3区域的cluster 3 (tumor core) 和 cluster 7 (tumor edge)通过通路富集分析展示了一个差异表达。在cluster 3观察到干扰素(IFIT1, IFITM1, IFITM3 and TAP1)、NK、嗜中性粒细胞(FCGR3B and TNFSF10)的信号通路上调。另外在这个区域RHOB上调表明了转移潜能。cluster 3区域代表这里的肿瘤生长受到免疫反应的抑制。在cluster 7 观察到TAMs、记忆B细胞(IGHG1, IGHG3,IGHG4, IGLC2 and IGLC3)和成纤维细胞(COL1A1, COL1A2, COL3A1, COL5A1,COL6A1, COL6A2 and FN1)的出现。组织蛋白酶(cathepsin)(CTSB, CTSD and CTSZ)活性和受体(C1QA, C1S)通路上调表明在这个区域有ATMs的促癌活性。肌动蛋白细胞骨架(actin cytoskeleton)信号的上调也说明的更高的转移潜能。此外组织蛋白酶激活和金属蛋白酶(metalloproteinase)(TIMP1 and TIMP3)抑制表明 对细胞外基质有完整性的干扰。
fig4D
很多因素被认为是肿瘤转移的驱动力，包括有pro-tumor微环境和肿瘤内CCI减少。使用PAGA表示肿瘤区域间的灰色线来追踪肿瘤转移过程。图中DCIS_LCIS_3被强烈关联到附近的IDC_6
然后用DCIS_LCIS_3去和所有的其他DCIS_LCIS做差异表达分析和富集，如图Sup.C，表明DCIS_LCIS_3有更多的免疫浸润(immune infiltrates)特别是TAMs肿瘤关联巨噬细胞，看fig4B。其他区域就cycling epithelial细胞较多。TAM浸润是总所周知的和实体瘤病人存活率低强相关的。因此我们又做了DCIS_LCIS_3到IDC_6的伪时序分析。因为这两个区域对应于SEDR的3、7、11 cluster，设置3是起点
总之，DESR分析可以揭示瘤内的异质性，发现伴随TAM浸润的tumor outer ring (cluster 7)，肿瘤相关的成纤维细胞cancer associated fibroblasts (CAFs),这些都是促进肿瘤生长的。SEDR也能显示从肿瘤核心区到附近侵入区的分子迁移图谱，表明了从pro-inflammatory到immune-suppressive微环境的迁移，这些促进肿瘤转移。


