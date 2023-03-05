---
title: A Unified Generative Framework for Various NER Subtasks
date: 2021-08-11 13:48
tags: 文献阅读
mathjax: true
---


核心思想：不做序列标注了，把ner任务转换成文本生成任务，用一个seq2seq模型去生成一个entity span sequence，这样就不需要设计复制的tagging模式了

seq2seq模型是用的BART

##### 原文
[A Unified Generative Framework for Various NER Subtasks](https://aclanthology.org/2021.acl-long.451)

<!-- more -->
## 创新点
下图模型架构中，decoder是自回归生成，初始输入一个\<s>

![fig2](https://tianchi-public.oss-cn-hangzhou.aliyuncs.com/public/files/forum/167802918331148091678029182457.png)

## Methods

### NER Task

输入为$ n $个tokens
$$ X = [x_1,x_2,...,x_n] $$
目标序列是
$$ Y = [s_{11},e_{11},...,s_{1j},e_{1j},t_1,...,s_{i1},e_{i1},...,s_{ik},e_{ik},t_i] $$
，其中$s,e$分别是span的开始和结束index，$t_i$是entity tag的index，用$G = [g_1,...,g_l]$表示entity tag tokens，这里$g_i$是token，令 $t_i \in (n,n+l]$


### Seq2Seq for Unified Decoding

$$ P(Y|X) = \prod_{t=1}^mP(y_t|X,Y_{<t})\tag{1} $$
其中$Y_{<t}$指$t$时刻以前的$Y$作为输入，也就是自回归。$y_t$是一个index不是token，就是一个编号，长度是句子长度加上entities类型的长度，即$n+l$

#### Encoder

$$ H^e = Encoder(X)\tag{2} $$
其中$H^e \in \mathbb{R}^{n \times d}$，d是隐藏层维数

#### Decoder 

for each step $P_t = P(y_t|X,Y_{<t}).$
$$ \hat y_t =
\begin{cases}
X_{y_t},& \text{if }y_t \le n,\\
G_{y_{t-n}},& \text{if }y_t > n\\
\end{cases}\tag{3} $$
简单的说这个式子就是把索引$y_t$转成token，因为前面说过，$[1,n]$是输入$X$的索引，$(n,n+l]$是entity tag索引，表示的是$G$

得到$\hat Y_{<t} = [\hat y_1,...,\hat y_{t-1}]$，然后经过Decoder后得到最后一层隐藏状态$h_t^d \in \mathbb{R}^d$
$$ h_t^d = Decoder(H^e;\hat Y_{<t}) \tag{4} $$

然后通过如下一系列公式得到index的概率分布$P_t$

$$
\begin{align}
E^e &= TokenEmbed(X) \tag{5}\\
\hat H^e &= MLP(H^e) \tag{6}\\
\bar H^e &= \alpha * \hat H^e + (1 - \alpha) * E^e \tag{7}\\
G^d &= TokenEmbed(G) \tag{8}\\
P_t &= Softmax([\bar H^e \otimes h_t^d;G^d \otimes h_t^d]) \tag{9}
\end{align}
$$

就是Fig2中的上面浅色框里的一系列计算过程，其中$TokenEmbed$是编码器解码器共享的，$E^e,\hat H^e,\bar H^e \in \mathbb{R}^{n \times d}$;$\alpha \in \mathbb{R}$是一个超参数；$G^d \in \mathbb{R}^{l \times d}$；$[\cdot ; \cdot]$表示在第一个维度连接(concat)两者；$\otimes$是点乘(dot product)


### Detailed Entity Representation with BART

BART最重要的就是一个tokenization的方法Byte-Pair-Encoding(BPE)，可以把一个token tokenize成几个BPEs
本文提出三种指针表示entities在原始句子中位置的方法，以更好地利用BPE，三种entity representation的方法如下：
都是BPE切割后，一个word可能形成好几个BPE，然后按顺序编index，见Fig.3

#### Span

把entity的首尾index放进去

#### BPE

把entity的每一个index放进去

#### Word

只放entity含有那个几个words的首位进去

![fig3](https://tianchi-public.oss-cn-hangzhou.aliyuncs.com/public/files/forum/167802919084449291678029190067.png)

## Results

后面实验做了三种entity类型的测试，看本文提出的方法的表现；
还看了三种不同entity representations的表现,“Word”表现最好。总结起来，两个原因影响表示方法的表现：1.entity的长度；2.和预训练任务的相似性。

后面还研究了仅仅用不连续实体样本去做评估、“word”在预测不做限制的条件下犯错误的概率，这里错误是invalid结构错误，以证明学习是有效的、句子中实体出现早晚对预测召回率的影响。

Results show that the entity representation with a shorter length and more similar to continuous BPE sequences achieves better performance.
