---
title: A Unified MRC Framework for Named Entity Recognition
date: 2021-08-27 12:09
tags: 文献阅读
---


把ner的序列标注问题做成是一个阅读理解的问题(MRC)。把训练数据变成(上下文，问题，答案)，训练的时候BERT的输入是把问题和上下文连起来用[SEP]分开，前后各加一个[CLS]。这就是为了和BERT保持一致。然后BERT的输出把第一句问题的表示向量给删了，只要后面上下文的表示向量。然后后面接了一个match model

<!-- more -->
### 两个关键点

 - 构造query
 - 怎么预测entity即match model

没有用自动生成，而是使用annotation guidelines note指导人工做的query

本文的span选择的策略上用的是两个二分类，对一句话的每个token都有两个二分类，是否是start index和是否是end index。这样导致一个问题就是最后会得到多个start和end，但是这些start和end怎么匹配不知道。所以后面又接了一个二分类，在得到start和end的index后，用一个分类器分类所有的匹配可能。

### 训练

三方面的loss：预测start的、预测end的、start和end匹配的，都是cross entropy

$$ \mathcal{L}_{start} = CE(P_{start},Y_{start}) \tag{1}$$
$$ \mathcal{L}_{end} = CE(P_{end},Y_{end}) \tag{1}$$
$$ \mathcal{L}_{span} = CE(P_{start,end},Y_{start,end}) \tag{2}$$
$$ \mathcal{L} = \alpha \mathcal{L}_{start} + \beta \mathcal{L}_{end} + \gamma \mathcal{L}_{span} \tag{3}$$

