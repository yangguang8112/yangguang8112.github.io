---
title: RoBERTa
date: 2021-01-30 12:33:38
tags: 文献阅读
---
主要是训练方法上的创新，该paper认为bert的训练明显不够，他们对超参调优和训练集大小的影响的仔细评估。提出RoBERTa这个recipe来提高BERT的训练效果。
<!--more-->
### 创新点
做的改变包括有：
1. 训练时间更长，batches更大，数据更多
2. 删除next sentence prediction objective
3. 在更长的句子上训练
4. 动态改变应用在训练数据上的masking pattern
   
本文还collect一个大的新数据集(CC-NEWS)，数量级和私密数据差不多，可以更好的控制训练集大小的效应。

贡献：
1. 提供了一组重要的BERT设计选择和训练策略可以使下游任务表现更好；
2. 使用了一个新数据集，证明使用更多的数据确实可以提高下游任务的表现；
3. masked language model pretraining在正确的设计选择下可以和最近发表的方法有竞争力。