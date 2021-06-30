---
title: CoLAKE
date: 2021-03-10 09:51:16
tags: 文献阅读
---
提出了一个方法可以同时做语言和知识的表征，并且在需要知识的任务中有较好的表现，在一些NLU（不太需要知识）的任务也没有降很多。
把输入的句子看成是一个全连接的graph，即word graph。然后根据句子中的实体到知识图谱中找一级三元组，只找对应实体的第一层邻居。然后将句子中的实体作为锚点（ancher node），以锚节点为中心抽出sub-knowledge-graph，然后根据锚点将sub-knowledge-graph和word graph合并。这是示意图是这样的，实际操作起来，在输入的时候这些node也是序列摆放，他们的graph结构是通过position embedding和mask矩阵来体现的。
<!--more-->
### 总结
可以深入研究的两个方向：
1. 预训练融入知识图谱，比如CoLAKE，K-BERT， KM-BERT
2. 三元组抽取那里做一些结构上的创新，比如casrel

最近主要是粗略的看了将知识图谱融入到预训练模型中的几篇文献，由近及远CoLAKE、K-BERT、KM-BERT到ERNIE-清华，其中KM-BERT是在bioBERT的基础上将医学领域知识图谱UMLS（The Unified Medical Language System）融合到预训练模型中，网络结构类似ERNIE-清华，后面下游任务的数据集和bioBERT中使用的一样，使用的数据集和知识图谱可以借鉴；上周跑了一下CoLAKE提供的代码，最近计划使用KM-BERT中提到的生物医学领域数据在CoLAKE上跑一下，加深对数据的认识。