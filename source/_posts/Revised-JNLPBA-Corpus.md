---
title: Revised JNLPBA Corpus
date: 2022-02-20 15:49
tags: 文献阅读
---

本文的动机是JNLPBA语料中有许多错误、漏标，导致下游的关系抽取不好做，所以本文总结了JNLPBA数据集中的错误（这些点也都出现在其他生物医学语料中），并重新纠正标注了这些错误，总结了重新标注的规范。
##### 原文
[Revised JNLPBA Corpus: A Revised Version of Biomedical NER Corpus for Relation Extraction Task](https://arxiv.org/abs/1901.10219)

<!-- more -->
## JNLPBA标注问题

### Problem of general terms
会把一些普通的词标注到生物医学实体中去，特定的实体必须是能在数据库里清晰的找到的，而不是一些大的生物医学概念。比如下面
_Substitution mutations in this **consensus sequence** eliminate binding of the **inducible factor**._
两个标黑的地方分别被误标为DNA和蛋白质了

### Unnecessary preceding words
实体前面一些没必要的修饰词，比如
_Expression of **dominant negative MAPKK-1** prevents NFAT induction._
dominant negative只是一种变异类型，不能和MAPKK-1一起标为蛋白质。不过也有些有必要的修饰词，文中提到human和murine作为物种可以对IL-2在数据库中对应基因ID起决定作用，所以这个中词要被划到实体里（**不过这里我认为，物种应该划为物种的实体，然后物种和基因有从属关系，这样才是细粒度的，对后面关系抽取也有益**）

### Entity type confusion
其实就是一些标错的，例子如下：
_“… that the type II IL-1R does not mediate gene activation in **Jurkat cells**.”_
_“…galectin-3 was shown to activate interleukin-2 production in **Jurkat T cells**.”_
这俩应该都是cell line，但是第二个因为多了个T被标为了cell type

### Neglected adjacent clues
也是标错的，根据实体周围的一些修饰词可以简单判断标错的
_“The 5' sequences up to nucleotide -120 of the human and murine **IL-16** genes …”_
IL-16这里被表为了蛋白质，但是后面有个genes，所以这里应该标为基因

### Missing annotations
漏标，大的语料库很难避免
_“Three additional smaller regions show homology to the **ELK-1** and SAP-1 genes…”_
通过查询GENIA ontology，发现**ELK-1**被漏标了

## 重新标注的Guideline
本文用了两位有生物+计算机背景的人分别做标注，然后综合结果

### 一般规则

1. **删除普通命名实体**，只要不能被关联到数据库中的任何ID的视为普通实体，比如_“upstream regulatory region”, “60-kDa protein” and “cytokines”_
2. **增加漏标的实体**，上文提到的
3. **调整命名实体类型**，上文提到的

### 特殊规则
有很多，最重要的一条是，当命名实体周围没有任何证据证明它的实体类型时，这个实体被标为蛋白质，比如_“An essential role for NF-kappaB in human CD34 ( + ) bone marrow cell survival.”_中，**NF-kappaB**不能根据上下文确定它的类型，则被标为蛋白质。因为执行生命过程的分子是蛋白质。

1. 形容词规则，同_unnecessary_preceding_words_
2. Verb rule Ving Event adjective verb rule，连接词前后有时候当作一个实体，有时候分别和最后一个词组成两个实体
3. Preposition rule，介词含在NE里的
4. Parenthesis rule，_“tumor necrosis factorprotein (TNFprotein)”_和_“interleukin (IL) -2protein”_
5. Conjunction rule，_“IL-1, 2, and 15”_会被标记为“IL-1protein , 2right_partial_protein, and 15right_partial_protein”
6. Semantic rule，将_“human gene PAX-5”_标注为_“human gene PAX-5DNA”_（存疑，见_unnecessary_preceding_words_）
7. Protein rule，关于蛋白质前后缀的，比如_“motif”、“domain”_是描述蛋白质的部分，而不是全部，所以不包含在蛋白质命名实体内，其他的关键词，如_protein, receptor, antigen, antibody, enzyme, (transcription) factor and kinase_以这些词结尾的都将被标为蛋白质。此外还有一些分子相关的线索的也将被标为蛋白质（应该是符合最重要的那条规则）
8. DNA rule，一些明显的描述DNA序列功能的后缀，比如_“enhancer”、“promoter”_是DNA的一部分；另外，比如_“AP-1 enhancer element”, “bcl-2 oncogene”, “gene UL49” and “FasL promoter”_也都是DNA；还有的包含染色体信息的DNA，比如_human chromosome 11p15, 1p36 and 14q11_；还有一些比较隐晦的，比如_“Pax-5 encodes the transcription factor BSAP which plays an essential role …”_，在这个句子中，**Pax-5**只有通过_encodes_推理得出只有DNA才具有编码能力（我估计这里没考虑RNA，或者把RNA和DNA视为一种）；最后还有一种特殊的描述克隆的DNA质粒的方式，比如_“pCD41”, “pIL-5 cDNA”_在实体前都有一个_“p”_
9. Cell rule，cell line的规则：1.有明显的cell line描述符号，_“Hela”, “Hep2” and “A549”_；2.常见的细胞名称，或者以关键词_“cell line” or “clone”_结尾的细胞形态、细胞功能描述，都视为cell line，比如_“T cell line”, “granulocytic clones” and “monocytic cell line”_。cell type的规则：提到特定的细胞类型，以_“cell”, “progenitor” or “precursors”_结尾的细胞形态和功能标注为cell type，比如_“thymocytes”, “hematopoietic cells” and “myeloid precursors”_
10. Complex rule，_“<Protein>/<Protein>”_这样的描述被标注为一个蛋白质，比如_“TCR/CD3”_被标注为_“TCR/CD3protein”_
11. Amino or DNA sequence rule，氨基酸/DNA序列不被标注为命名实体，比如_“WGATAR consensus motifs” and “GGAAAGTCCC”_
12. Group/family protein，虽然蛋白质家族不在Entrez/UniProt数据库中，但是因为他们在RE中很重要，所以本文还是标注出它们
