---
title: KEPLER
date: 2021-05-20 10:07:47
tags: 文献阅读
---
> KEPLER: A Unified Model for Knowledge Embedding and Pre-trained Language Representation

这篇论文提出一种模型能统一知识embedding和预训练语言表示（Knowledge Embeddingand Pre-trained LanguagE Representation (KEPLER)）
<!--more-->
##### 贡献
1. 提出KEPLER，一个知识增强的PLM，联合优化了KE和MLM；
2. 通过encoding文本描述作为实体embedding，KEPLER表现出它作为一个KE模型的高效；
3. 还提出了Wikidata5M，一个新的KG数据集，这将会推动大尺度KG的研究，inductive KE,以及KG和NLP之间的交互
   
### 模型
<img src="\img\20210520-152509.png">

#### Encoder
用的transformer和bert那篇文章里的一样。输入N个tokens的seq，输出每个token的向量表示。tokenizer可以把原始文本转化成tokens序列，这里用的是和RoBERTa里一样的tokenizer(the Byte-Pair Encoding(BPE))
这里encoder没有像一些其他知识增强的工作一样去修改transformer，去额外加一些实体链接层或者知识集成层，减少了额外推理开销，下游应用也和使用RoBERTa一样简单。

#### Knowledge Embedding
定义KGs：用三原组(h,r,t)描述关系事实，一个KG(knowledge graph)是实体为节点、关系为边的graph。传统的KE模型都是给每个实体和关系分配一个d维向量，然后定义一个打分函数去训练embeddings和predicting links
KEPLER里，通过实体对应的文本把实体编码成向量，而不是使用stored embeddings
三个方法：
1. entity descriptions as embeddings
2. entity and relation descriptions as embeddings
3. entity embeddings conditioned on relations
   
#### Masked Language Modeling
MLM继承自BERT和RoBERTa。初始化预训练使用RoBERTaBASE的checkpoint。当在训练KE objective时候保持MLM也作为objectives之一避免灾难性遗忘。在后面5.1节中证明，只用KE作为目标会导致NLP任务有较差的结果

#### Training Objectives
如图2中所示，总的KE loss加上MLM loss就是训练的总的loss，这样在保留PLMs的句法语义理解的同时也把把KGs融入到text encoder里了。注意，这里两个任务只共享text encoder，对于每个mini-batch，KE和MLM的text data采样不一定相同。这样可以让MLM看到更多不一样的text而不仅仅是对于实体的描述。

#### Variants and Implementations

### 数据 Wikidata5M
#### Data Collection
对于Wikidata中的每一个实体，把它对齐到它的Wikipedia页面并且提取第一节作为它的描述。没有页面和描述少于五个words的实体舍弃。检索Wikidata中的所有relational facts，当它的头实体和尾实体都存在且它的关系在Wikidata中有页面时被认定为有效。
<img src="\img\20210525-t1-161804.png">
<img src="\img\20210525-t2-161823.png">

#### Data Split
两种数据分割方式
##### transductive setting
就是表1里展示的那样，大多数的KG数据集也是采用的这样的方法。其中实体是共享的，三元组集合在训练集、验证集和测试集中是不相交的。在这种情况下KE模型只能对训练集中的实体学到有效的embeddings

##### inductive setting
是表三里展示的那样
<img src="\img\20210525-t3-165112.png">
实体和三元组在训练集、验证集和测试集中是相互分离的，随机抽取一些连通子图作为验证集和测试集。 **这里没看懂**
Wikidata5M包含有大量的实体和三元组，但是验证集和测试集很小是因为受到了连接预测标准评价方法的限制（貌似是KE score的计算需要数据集中总的实体数和三元组数相乘再乘2），所以为了evaluation就限制了测试集三元组的大小。 **这样表明大数据量的KE急需要一个更有效的评估方法。**

#### Benchmark
用一些以前的KE模型跑了Wikidata5M，有三种评价指标，不是很懂，大概就是计算测试集里每一个三元组，先固定头实体预测尾实体，然后反过来。
<img src="\img\20210526-t4-153450.png">

### 训练
### 结果