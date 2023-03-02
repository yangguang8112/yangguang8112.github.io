---
title: Automatic cell type identification methods for single-cell RNA sequencing
date: 2021-12-22 20:23:08
tags: 文献阅读
---


### 任务描述
Xie B et al(2021)描述了一个unlabel rate以及肿瘤相关的specificity和sensitivity，具体来说是评估一个模型能不能预测unlabel cell type以及，当用正常组织细胞训练，对预测肿瘤细胞做预测，将unlabel的预测结果看作是恶性细胞。

<!--more-->
### 工具比较
关于上述任务，在Tabula Muris lung data数据集上评估，clustifyr有最高的specificity和sensitivity，但是在正常lung data上表现（F1 score）不是最好的。因为有一些方法不预测unlabel，所以不做这个评估。在正常组织中表现很好的CellFishing.jl在肿瘤评估中不行

![20211210-132551.png](https://tianchi-public.oss-cn-hangzhou.aliyuncs.com/public/files/forum/167776349463640441677763494114.png)


### 我的实验
首先对cell-blast和clustifyr做测试，超算上的路径为/lustre/home/acct-medcyn/medcyn-user9/ygWork/scRNA/cell_anno
### 小结

1. 将unlabel的结果视作恶性细胞是否合理；
2. 

## Probabilistic cell-type assignment of single-cell RNA-seq for tumor microenvironment profiling

1. 概率模型的核心应该是如何评估marker基因在细胞中的overexpress
2. 模拟数据是怎么做的，还模拟出了差异表达程度(0.05-0.45)
3. Fig2f什么意思

## scmap
title: scmap: projection of single-cell RNA-seq data across data sets
将数据投影到参考数据集上以注释细胞类型。任务目标就是把一个新细胞c比对到参考数据的某一个最相似的cluster上或者某一个细胞上，对应两个方法。
_Conceptually, such projections are similar to the popular BLAST2 method, which quickly finds the closest match in a database of nucleotide or amino acid sequences._

### 方法

#### scmap-cluster
搜索一个最相似的cluster到细胞c

1. cluster用质心来表示，即cluster内的所有细胞每个基因的中位数构成的一个向量，然后计算和细胞c之间的相似度
2. 使用无监督学习选取最相关的部分基因来计算相似度；（文中说这样就可以克服批次效应）
3. 上述特征选取使用三种不同的策略：随机、高频突变基因（HVGs）、genes with a higher number of dropouts (zero expression) than expected (determined using M3Drop)）
4. 相似度计算：cosine similarity 、 Pearson 和 Spearman correlations，至少两个大于0.7，否则为unassigned

#### scmap-cell
搜索一个最相似的细胞到细胞c

1. 因为细胞数量很大，上面cluster的直接最近邻的方法不能用，为了加速最近邻细胞搜索，使用quantizer近似加速；
2. 和上面一样做了特征选择
3. 近似最近邻搜索，只用cosine similarity来分一个细胞的k近邻，只有当3-NN是相同的细胞类型且最大的相似度大于0.5则认为此细胞是assigned

### 实验验证
使用[17个公开数据集](https://hemberg-lab.github.io/scRNA.seq.datasets)
评估方法：Cohen’s κ (ref. 6)，预测结果一致性指标，如果值为1表示预测的细胞类型和原始的完全一致

#### self-projection
We randomly selected 70% of the cells from the original sample for the reference, and projected the remaining 30%, with clusters as defined by the original authors.
为了评估特征选择方法好坏
最后结论就是dropout-based的特征选择方法表现最好。令人奇怪的是有些随机选择的表现比HVG方法强。
然后使用RF和SVM做监督学习分类细胞，在三种特征选择的方法上均略好于scmap cluster/cell

#### positive controls
we considered seven pairs of data sets that we expected to correspond well on the basis of similar sample origins.

#### negative controls
we projected data sets with origins not related to the reference (e.g., retina onto pancreas

## CellFishing.jl
**速度、准确**

### workflow
digital gene expression (DGE) matrix
![20211201-190425.png](https://tianchi-public.oss-cn-hangzhou.aliyuncs.com/public/files/forum/167776351912878791677763518912.png)
 首先用reference cells的表达量构建一个搜索数据库，然后用相似表达模式来查询细胞。

#### preprocess
由五步组成：

1. feature (gene) selection
2. cell-wise normalization
3. variance stabilization
4. feature standardization
5. dimensionality reduction
#### hash
#### index

### 数据集
![20211201-185731.png](https://tianchi-public.oss-cn-hangzhou.aliyuncs.com/public/files/forum/167776352682897811677763526639.png)

## Cell Blast
使用参考单细胞转录组数据训练一个非监督模型，可以把高维转录空间映射到低维细胞embedding空间，并且使用adversarial alignment的方法校正intra-reference batch effect
![20210624-143842.png](https://tianchi-public.oss-cn-hangzhou.aliyuncs.com/public/files/forum/167776353856792871677763538045.png)
然后在query data的时候，cell blast用预训练好的非监督模型把单个query cell映射成embedding，然后利用在低维空间的后验分布（which characterizes the uncertainty of cell embeddings）去估计cell2cell的相似度
![20210624-151043.png](https://tianchi-public.oss-cn-hangzhou.aliyuncs.com/public/files/forum/167776355196267091677763551727.png)
这里没有直接拿cell embedding去和reference做相似度
### ACA
基于公开的单细胞测序数据集，高质量、统一、高分辨率细胞类型描述，总共包含2989582个细胞。横跨8个物种5个whole-organism atlases覆盖27种器官。上面用的模型就是在整个ACA上训练的。Notably, we found that the model works well in most cases with minimal hyperparameter tuning (cell-embedding visualizations, self-projection coverage and accuracy available on our website, Supplementary Fig. 18b).好像制作了self-projection来评估？
![20211202-094022.png](https://tianchi-public.oss-cn-hangzhou.aliyuncs.com/public/files/forum/167776374426773791677763744044.png)

## Deep generative model embedding of single-cell RNA-Seq profiles on hyperspheres and hyperbolic spaces

![20211202-100901.png](https://tianchi-public.oss-cn-hangzhou.aliyuncs.com/public/files/forum/167776357745729141677763576978.png)
![20211202-100914.png](https://tianchi-public.oss-cn-hangzhou.aliyuncs.com/public/files/forum/167776358582062151677763585284.png)

## Efficient and precise single-cell reference atlas mapping with Symphony
![20211202-100804.png](https://tianchi-public.oss-cn-hangzhou.aliyuncs.com/public/files/forum/167776359782193951677763597045.png)


 
### 参考文献
> Xie B, Jiang Q, Mora A, et al. Automatic cell type identification methods for single-cell RNA sequencing[J]. Computational and Structural Biotechnology Journal, 2021.

> Fu R, Gillen AE, Sheridan RM, Tian C, Daya M, Hao Y, Hesselberth JR, Riemondy KA. clustifyr: an R package for automated single-cell RNA sequencing cluster classification. F1000Res. 2020 Apr 1;9:223. doi: 10.12688/f1000research.22969.2. PMID: 32765839; PMCID: PMC7383722.

> Sato K, Tsuyuzaki K, Shimizu K, Nikaido I. CellFishing.jl: an ultrafast and scalable cell search method for single-cell RNA sequencing. Genome Biol. 2019 Feb 11;20(1):31. doi: 10.1186/s13059-019-1639-x. PMID: 30744683; PMCID: PMC6371477.



