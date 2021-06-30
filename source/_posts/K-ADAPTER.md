---
title: K-ADAPTER
date: 2021-04-22 09:57:30
tags: 文献阅读
---
大多数之前的工作通过多任务学习来注入知识和更新模型参数以增强预训练语言模型。不管他们用了哪些方法做知识注入，遇到的共同问题就是对前面知识的灾难性遗忘。figure1a
figure1b是我们提出的K-ADAPTER，不同类型的知识被注入到不同的袖珍神经网络模型（比如本文中的adapter），他们之间互相独立，而不是直接注入到预训练模型。这样既能固定预训练模型的原始表示，又支持继续知识注入。adapter是一个含有特定知识的模型，是独立于预训练模型之外的插件。
<!--more-->
### 模型 K-ADAPTER
#### Adapter Structure
<img src="\img\20210422-103225.png">
adapter的结构如上，一个adapter模型是由K个adapter layer组成，每个layer包含N个transformer和两个projection layer。与预训练模型的连接方式：每个adapter layer和预训练模型的不同的transformer layer连接，具体是，一个adapter layer的输入为上一个adapter layer的输出特征连接上预训练模型中transformer layer的隐藏特征。对于每一个adapter，连接预训练模型和adapter的最后一个隐藏层特征作为这个adapter模型的输出特征。

### 训练方法
不同的adapter用不同的预训练任务分别训练。
#### Pre-training settings
使用RoBERTa<sub>LARGE</sub> (L=24, H=1024, A=16, 355M params)作为预训练模型。
adapter结构中，N表示transformer层数，Ha表示transformer隐藏层维数，Aa表示self-attention的head数，Hd和Hu分别表示down-projection和up-projection的隐藏层维数。在adapter中N = 2, Ha = 768, Aa = 12, Hu = 1024 and Hd = 768. adapter被插到RoBERTa的{0,11,23}这些层，不共享参数，每个adapter的参数大约42M。
#### Factual Adapter
从T-REx抽取一个子数据集T-REx-rc，T-REx是一个大尺度的对齐数据集，对齐Wikipedia abstracts and Wikidata triples，舍弃少于50个实体对的关系，文章收集了430个关系和5.5M句子。使用关系分类任务来训练。已经把实体标注了，只分类关系。
> Specifically,the last hidden features of RoBERTa and facAdapter are concatenated as the input representation, and the pooling layer is applied to the input representations of the given entities. Then, we concatenate two entity representations to perform relation classification.

#### Linguistic Adapter
Linguistic知识是蕴含在句法、语义信息中的。这次我们构造一个1M例子组成的数据集，
> In particular, we run the off-the-shell dependency parser from Stanford Parser3 on a part of Book Corpus (Zhu et al., 2015).

使用依赖关系预测任务来训练linAdapter，就是预测输入句子每个token的head index（我估计就是那些语法标签吧）。具体地，也是连接RoBERTa and linAdapter的最后一层隐藏特征作为输入，应用在一个线性层linear layer对每个token做一个分类。

### 实验结果