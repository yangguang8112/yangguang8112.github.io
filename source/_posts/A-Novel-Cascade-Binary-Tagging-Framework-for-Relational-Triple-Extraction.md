---
title: A Novel Cascade Binary Tagging Framework for Relational Triple Extraction
date: 2020-08-6 15:55:55
tags: 文献阅读
---
https://kexue.fm/archives/6671 这篇博客是在这篇文献之前写的，思想都差不多，可以参考
### 主要创新思想
以一个新的角度去提取关系三元组，以前的工作都是把关系看作一个离散的标签，而本文的框架(CasRel)则将一个句子里的关系建模为subjects到objects的函数。这个方法还能自然解决关系重叠的问题。

### 主要原理剖析及说明

<img src="\img\20200730-085539.png">

CasRel框架主要由三个部分组成，分别是BERT Encoder, Subject Tagger和Relation-Specific Object Taggers
其中BERT Encoder是基于多层双向Transformer的语言表示模型。主要的作用就是将句子中的token表示(编码)成向量。这里面具体是先用了WordPiece来做embbedding
<img src="\img\20200730-091202.png">
其中S表示输入句子在sub-words(具体是属于WordPiece Embedding)中索引的one-hot向量组成的矩阵，Ws表示sub-words的词嵌入矩阵，Wp是位置的词嵌入矩阵，p是指在输入句子中的位置索引。hα是隐变量(隐藏的状态向量??)，也就是输入句子在第α层的Transformer blocks(总共N层)的语义表示。
一个Subject Tagger和一组Relation-Specific Object Taggers构成了Cascade Decoder. 首先我们从输入的句子中识别所有的subjects，然后检查每一个候选的subject在所有的关系类型中是否有有联系的object

其中Subject Tagger是用来识别输入句子中尽可能多的subjects，它的输入是N层BERT encoder产生的hN向量。
<img src="\img\20200730-110830.png">

### 主要实验结果
###### 代码、实现、复现、配置
NYT 训练了8个epoch或者更多后的测试结果(因为colab显示原因，看不到输出了，反正最后也没跑完)
``` bash
Partial Match
0it [00:00, ?it/s]2020-08-02 10:05:15.442952: I tensorflow/stream_executor/platform/default/dso_loader.cc:44] Successfully opened dynamic library libcublas.so.10
5000it [02:27, 33.86it/s]
correct_num:7309.0000000001
predict_num:8374.0000000001
gold_num:8110.0000000001
0.8728206352997389	0.9012330456226892	0.8867993205532653
```
NYT数据量太大了，在colab上用16G显卡(Tesla P100)跑一个epoch需要四十分钟到一个小时；所以只跑了WebNLG数据.这是在WebNLG数据集上batch_size=6,epoch_num=100,max_len=100，实际训练到57个epoch就停止了.
``` bash
Epoch 57/100
836/836 [==============================] - 113s 136ms/step - loss: 0.0050
500it [00:13, 37.20it/s]
correct_num:1023.0000000001
predict_num:1137.0000000001
gold_num:1113.0000000001
f1: 0.9093, precision: 0.8997, recall: 0.9191, best f1: 0.9172

Epoch 00057: early stopping
```
下面是在总的测试集上的分数，如果需要按paper里面的分overlap类型和实体个数跑，可以直接在run.py里改脚本。
``` bash
correct_num:1450.0000000001
predict_num:1591.0000000001
gold_num:1581.0000000001
0.9113764927718472	0.9171410499683796	0.9142496847414935
```
