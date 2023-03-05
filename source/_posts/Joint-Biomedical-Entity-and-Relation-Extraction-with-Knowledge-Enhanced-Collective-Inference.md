---
title: >-
  Joint Biomedical Entity and Relation Extraction with Knowledge-Enhanced
  Collective Inference
date: 2021-08-27 12:05
tags: 文献阅读
---


提出一个新的利用外部知识的联合抽取框架KECI (Knowledge-Enhanced Collective Inference)

## 要点

分三步：

1. 将input text结构化为初始化图(initial span graph)，作为对输入文本的初步理解。在一个span graph里每个node表示一个entity，每条edge表示两个entity的relation
2. 然后在一个额外的知识库里使用entities linker形成一个包含所有潜在有关联的生物医学实体的背景知识图(background knowledge graph)，对于每个entity从上面提到的知识库(KB)里提取它的semantic types，definition sentence以及relational information
3. 最后KECI使用注意力机制融合上述两个graph成一个更refined的graph

<!-- more -->

![overview](https://tianchi-public.oss-cn-hangzhou.aliyuncs.com/public/files/forum/167803168007465281678031679132.png)

几个问题：

- ~~entities linker是怎么做的~~ 使用了一个工具叫MetaMap
- ~~怎么融合的？用的GCN？~~ 是用的注意力机制，soft-align两个graph里的entities
- ~~background knowledge graph是啥~~ 是UMLS

## Methods

### 第一步

#### span encoder

这里的span有点像是词组或者说就是候选entity，$X = (x_1,...,x_n)$是sciBERT的输出，n是输入的document(D)的token数，$x_n$就是token$~i$对应的向量表示
$$ s_i = FFNN_g([x_{START(i)},x_{END(i)},\hat{x_i},\phi(s_i)])\tag{1} $$
其中$s_i \in \mathbb{R}^d$代表一个span的向量表示，$START(i) END(i)$表示$s_i$的开始和结束索引。$\hat{x_i}$就是span里的token的attention-weighted的和，$\phi(s_i)$是span长度的特征向量。$FFNN_g$是一个含有ReLU激活层的ffn

#### Initial Span Graph Construction

然后同时预测每个span的类型以及两个span之间的关系，$E$表示所有的entity类型，$R$表示所有关系类型，都包含了None这种类型在里面。

首先给span分类：
$$ e_i = Softmax(FFNN_e(s_i))\tag{2} $$
$FFNN_e$让维度从d变为E($\mathbb{R}^d \rightarrow \mathbb{R}^E$)，然后使用另一个softmax做span pair的关系分类$\langle s_i,s_j\rangle$

$$ r_{i,j} = Softmax(FFNN_r([s_i,s_j,s_i \circ s_j]))\tag{3} $$
$\circ$表示element-wise multiplication

用$r_{ij}[k]$表示$s_i$和$s_j$是关系$k$的可能性 $\mathbb{R}^{3 \times d} \rightarrow \mathbb{R}^{|R|}$

**以上都是普通的实体识别和关系抽取**

在进行下一步前还做了一个除了，剪枝不太可能的entities以提高计算效率，经过筛选的结果去用GCN做成initial span graph，筛选的策略是只保留有最低non-entity可能性的$\lambda n$个spans，其中$\lambda$是经验值，本文取0.5

对于每对span$\langle s_i,s_j \rangle$建一个从节点$s_i$到$s_j$之间的有向边$|R|$，每个边表示一种关系类型，边上的权重就是$r_{ij}$

用$G_s = \{V_s,E_s\}$表示initial span graph，使用双向GCN去迭代更新每个span的表示


$$  \tag{4}$$


三个公式迭代多次，然后每个span($s_i$)就包含了整个graph($G_s$)的信息，用$h_i$表示GCN最后一层的特征向量，$h_i \in \mathbb{R}^d$

### 第二步

#### Background Knowledge Graph Construction

本文用的外部知识库是Unified Medical Language System(UMLS)，由三部分组成：

> Metathesaurus, Semantic Network, and Specialist Lexicon and Lexical Tools

其中Metathesaurus里有数百万细粒度的生物医学概念以及他们之间的关系，为了统一我们使用UMLS里的概念作为entities。每个entity会被注释成至少一个higher-level semantic types，比如说解剖结构、细胞或者是病毒。除了有entities之间的关系外还有semantic types之间的关系。比如

> there is an affects relation from Acquired Abnormality to Physiologic Function

这些信息都是由Semantic Network提供的。

**构建背景知识图的具体操作为**：首先使用MetaMap(UMLS的entity mapping工具)根据输入$D$提取UMLS里的biomedical entities；然后每个entity作为一个节点，每个entity的semantic types作为关联节点；最后在Metathesaurus和Semantic Network中找到相关关系。如fig2中所示，阴影部分就是背景知识图，圆圈是entity节点，方框是关联的semantic types节点。这里跑MetaMap的时候没有调任何参数，所以会map到一些不相关的entity，后面会讲KECI是如何学会忽略这些不相关的实体的。

用$G_k = \{V_k,E_k\}$表示结构化的background KG，$V_k E_k$分别表示节点和边的集合，使用一个UMLS的预训练embedding[1]初始化$V_k$中每个节点的向量表示。然后使用SciBERT把每个节点在UMLS中的definition sentence编码成一个向量，然后把这个向量连接到前面初始化的向量上。然后就是用GCN迭代整个graph把所有的node都更新一边，这里因为$G_k$是一个异构关系图(heterogeneous relational graph)，所以使用的是一个relational GCN[2]
$$ v_i^{l+1} = ReLU\left(U^{(l)}v^l_i+\sum_{k \in R} \sum_{v_j \in N^k_i}\left(\frac{1}{c_{i,k}}U_k^{(l)}v_j^l\right)\right)\tag{5}$$
其中$v_l^i$是$v_i$在l层的特征向量，$N^k_i$是$v_i$在$k \in R$关系下的邻居节点集合，$c_{i,k}$是一个归一化常数，$c_{i,k}=|N^k_i|$

经过多轮迭代后KG的关系信息会被嵌入到每个节点的表示里，$v_i$表示relational GCN最后一层的特征向量，然后用一个简单的神经网络将$v_i$映射到想同维度的$n_i \in \mathbb{R}$




### 第三步

#### Final Span Graph Prediction

现在就是要把两个graph中的entities通过注意力机制soft-align

$$ \alpha_{ij} = FFNN_c\left([h_i,n_j]\right)\tag{6} $$
$$ \boldsymbol{c_i} = FFNN_s(h_i) \tag{7} $$
$$ \alpha_i = FFNN_c\left([h_i,\boldsymbol c_i]\right) $$


$$ Z = exp(\alpha_i) + \sum_{v_z \in C(s_i)}exp(\alpha_{iz})\tag{8}$$
$$ \beta_i = exp(\alpha_i)/Z ~~and~~ \beta_{ij} = exp(\alpha_{ij})/Z $$
$$ \boldsymbol f_i = \beta_i\boldsymbol c_i + \sum_{v_z \in C(s_i)}\beta_{ij}\boldsymbol n_j $$
最后对于每一个span $s_i$得到最终的knowledge-aware的表示$\boldsymbol f_i$

然后通过和公式1公式2一样的方法做分类
$$ \widehat{\boldsymbol e_i} = Softmax\left(FFNN_{\widehat{e}}\left(\boldsymbol f_i\right)\right)\tag{9}$$
$$ \widehat{\boldsymbol r_{ij}} = Softmax\left(FFNN_{\widehat{r}}\left([\boldsymbol f_i,\boldsymbol f_j,\boldsymbol f_i \circ \boldsymbol f_j]\right)\right) $$
$\widehat{\boldsymbol e_i}$和$\widehat{\boldsymbol r_{ij}}$就是最终预测结果


#### Training

$$ \mathcal{L}_{total} = (\mathcal{L}_1^e + \mathcal{L}_1^r) + 2(\mathcal{L}_2^e + \mathcal{L}_2^r)\tag{10} $$
其中 $ \mathcal{L}_*^e $ 表示span分类的cross-entropy的loss

$ \mathcal{L}_*^r $表示关系分类的cross-entropy的loss，下表1和2分别表示的是公式2、3和公式9

end-to-end训练只使用了命名实体识别和关系抽取的ground-truth标签，没有使用任何entity linking supervision

## 参考

> [1] Maldonado, Meliha Yetisgen, and Sanda M. Harabagiu. 2019. Adversarial learning of knowledge embeddings for the unified medical language system. AMIA Joint Summits on Translational Science proceedings. AMIA Joint Summits on Translational Science, 2019:543–552

> [2] Schlichtkrull, Thomas Kipf, P. Bloem, R. V. Berg, Ivan Titov, and M. Welling. 2018. Modeling relational data with graph convolutional networks. ArXiv, abs/1703.06103.

