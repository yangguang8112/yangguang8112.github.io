---
title: BioNLP数据集整理
date: 2021-09-23 11:25
tags: 文献阅读
---

![20210830-151458.png](https://tianchi-public.oss-cn-hangzhou.aliyuncs.com/public/files/forum/167800228884052021678002288767.png)
图片来自于“Domain-Specific Language Model Pretraining for Biomedical Natural Language Processing”

<!-- more -->
## NER

1. BC5-chem
2. BC5-disease
3. NCBI-disease
4. BC2GM
5. JNLPBA

### BC5-Chemical & BC5-Disease
本来这个数据集是用来评估drug-disease interactions的做关系抽取的，但是被频繁用来作为chemical (drug) and disease实体识别的语料。该数据集包含1500篇PubMed摘要，被分成三部分，训练集、验证集和测试集。
一般来说大家都是按照Crichton et al.[1]的预处理方法，把关系标签丢掉，分别在BC5-Chemical和BC5-Disease上训练chemical和disease的NER模型[2]。

### NCBI-Disease
包含793篇PubMed摘要，其中有6892个标注疾病，被链接到790个疾病实体。按照Crichton et al.[1]来分割数据集。

### BC2GM
关于gene的数据集。由含有人工标注的gene and alternative gene entities的PubMed摘要组成。BC2GM中有15000个训练句子和5000个测试句子。按Crichton et al.的做法主要关注gene的标注，并从训练集中取出2500个句子作为验证集。

### JNLPBA
也是一个基于PubMed的NER数据集，它最早发布在一个biomedical NLP的workshop的shared task。实体类型主要是分子生物学领域，蛋白质、DNA、RNA、cell line和细胞类型。
其中有些实体类型的区分不是很有意义，比如，“a gene mention often refers to both the DNA and gene products such as the RNA and protein”，Gu et al[2]认为一个基因通常是指的DNA或基因的产物比如蛋白质或RNA。因此他们把这个数据集只按照是不是实体，而不区分实体类型了。

## RE

1. ChemProt
2. DDI
3. GAD

### ChemProt
ChemProt基于PubMed摘要，是标注有chemical-protein互作关系的数据集。总共有23种互作类型，更高一级是10类包括无关系。在ChemProt中有关系的实例大部分都是在一个句子里。根据以前的一些工作就只需要考虑句子级别的关系实例。ChemProt作者建议主要关注五类互作类型即，UPREGULATOR (CPR : 3), DOWNREGULATOR (CPR : 4), AGONIST (CPR : 5), ANTAGONIST (CPR : 6), SUBSTRATE (CPR : 9) 以及everything else (false)
Chemprot注释并不完全适用于所有的chemical-protein pairs，根据以前的一些工作[3, 4]we expand the training and development sets by assigning a false label for all chemical-protein pairs that occur in a training or development sentence, but do not have an explicit label in the ChemProt corpus. Note that prior work uses slightly different label expansion of the test data.

### DDI
药物互作数据集，是用来助力研究药物信息抽取的，特别关注在药物安全上。它是基于PubMed摘要的句子级注释。注意一些以前的工作[4, 5]丢弃了90个训练文件，因为作者任务那些对ddi的学习没有用处。

### GAD
The Genetic Association Database遗传相关数据库，是利用[Genetic Association Archive](https://geneticassociationdb.nih.gov/)半自动创建。档案包含了一个gene-disease关联列表，对应的句子都是在PubMed摘要上报告了的相关研究。
Bravo et al.[6]使用一个生物医学NER工具识别基因和疾病，根据档案里存在的被注释的句子创建了正样本，negative examples from gene-disease co-occurrences that were not annotated in the archive

## BioRelEx 1.0
待更新......

## 参考
[1] Gamal Crichton, Sampo Pyysalo, Billy Chiu, and Anna Korhonen. 2017. A neural network multi-task learning approach to biomedical named entity recognition. BMC bioinformatics 18, 1 (2017), 368.
[2] Gu Y, Tinn R, Cheng H, et al. Domain-specific language model pretraining for biomedical natural language processing[J]. arXiv preprint arXiv:2007.15779, 2020.
[3] Jinhyuk Lee, Wonjin Yoon, Sungdong Kim, Donghyeon Kim, Sunkyu Kim, Chan Ho So, and Jaewoo Kang. 2019. BioBERT: a pre-trained biomedical language representation model for biomedical text mining. Bioinformatics (09 2019). [https://doi.org/10.1093/bioinformatics/](https://doi.org/10.1093/bioinformatics/) btz682
[4] Yifan Peng, Shankai Yan, and Zhiyong Lu. 2019. Transfer Learning in Biomedical Natural Language Processing: An Evaluation of BERT and ELMo on Ten Benchmarking Datasets. In Proceedings of the 18th BioNLP Workshop and Shared Task. Association for Computational Linguistics, Florence, Italy, 58–65. [https://doi.org/10.18653/v1/W19-5006](https://doi.org/10.18653/v1/W19-5006)
[5] Yijia Zhang, Wei Zheng, Hongfei Lin, Jian Wang, Zhihao Yang, and Michel Dumontier. 2018. Drug–drug interaction extraction via hierarchical RNNs on sequence and shortest dependency paths. Bioinformatics 34, 5 (2018), 828–835.
[6] Àlex Bravo, Janet Piñero, Núria Queralt-Rosinach, Michael Rautschka, and Laura I Furlong. 2015. Extraction of relations between genes and diseases from text and large-scale data analysis: implications for translational research. BMC bioinformatics 16, 1 (2015), 55.
