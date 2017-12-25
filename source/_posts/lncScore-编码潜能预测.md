---
title: 'lncScore:编码潜能预测'
date: 2017-07-08 22:52:37
tags: 文献
---
这篇文献主要介绍了lncScore，用python写的一个脚本,主要是依赖一个机器学习第三方库scikit-learn。它能够通过开放阅读框,外显子和最大编码子序列等11个特征参数对lncRNA进行筛选。为了加快lncScore的运行速度，主要采用多线程>分析，只需花费2分钟的时间就能够对64,756个转录本进行分类。
文章里用gencode数据库里的lncRNA数据做了验证
此工具与CPAT, CNCI 和 PLEK类似，我们的lncRNA流程里的编码潜能预测软件用的是CPC CNCI Pfam，貌似CPC也是这个团队开发的。有时间把这几个软件都看一下。
<!--more-->
#### 安装环境
##### 安装 scikit-learn
``` bash
pip install numpy
pip install scipy
pip install sklearn
```
##### 下载 lncScore
``` bash
git clone https://github.com/WGLab/lncScore.git
```

#### 测试数据集
##### 使用方法
``` bash
python lncScore.py \
	-f test/human_test.fa \  #fa文件;
	-g test/human_test.gtf \  #gtf文件;
	-o result \  #输出结果文件名;
	-p 1 \  #指定线程数;
	-x dat/Human_Hexamer.tsv \  #hexamer频率表,通过tools/make_hexamer_tab.py准备;
	-t dat/Human_training.dat \  #参考数据集,通过tools/make_TrainingDat.py准备;
```

