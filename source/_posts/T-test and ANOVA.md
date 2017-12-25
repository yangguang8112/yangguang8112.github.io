---
title: T-test and ANOVA
date: 2017-03-05 21:17:27
tags:
---
针对目前转录组及lncRNA的分析，我们的信息搜集表大部分都是针对的是两个组别两个样品之间的差异分析，但是对于一些实验设计，两两比较往往不能满足老师的分析要求。比如有的老师会做一个持续时间变化的实验设计，两两比较可以比较两个时间点的差异基因，但是对于一个持续时间段的差异基因的变化。又比如医口项目，老师有几对样品。正常和病变的，两两比较可以看出两个样本之间的差异，但是对于正常和病变两类之间差异比较，也可以选择其他统计学比较检验方式。下面就根据我个人做过的项目售后和个性化的经验，讲解一下曾经用过的几种差异比较的方式;
<!--more-->
## 1 T-test 
T 检验又称student's test ，在研究中最常见的行为对两组之间进行比较。比如医口项目中正常组织和病变组织的基因或转录本表达的差异比较。
T检验的基本原理是：首先假设零假设H0成立，即样本间不存在显著差异，然后利用现有样本根据t 分布求得t值，并据此得到相应的概率值p，若p≤α，则拒绝原假设，认为两样本间存在显著差异。 
### 1.1 配对样本T检验 
配对T检验：将受试对象的某些重要特征按相近的原则配成对子，目的是消除混杂因素的影响，一对观察对象之间除了处理因素/研究因素之外，其它因素基本齐同，每对中的两个个体随机给予两种处理。
配对样本T检验的前提要求是：
1、两个样本应是配对的。首先两个样本的观察数目相同，其次两样本的观察值顺序不能随意改变。
2、样本来自的两个总体应服从正态分布。
 以之前做过的一个项目为例
| 组合   | 处理组           | 对照组             | 描述      |
| -- | -- | -- | -- |
| 组合4  | FC6,ZS6,JYS2  | FC6T,ZS6T,JS2T  | 独立样本检验  |
| 组合5  | FC6,ZS6,JYS2  | FC6T,ZS6T,JS2T  | 配对样本检验  |
| 组合6  | FC6,ZS6       | FC6T,ZS6T       | 配对样本检验  |
| 组合7  | FC6,JYS2      | FC6,JYS2T       | 配对样本检验  |
``` r
setwd ("/PROJ/RNA/07/Regcon/NH150605_ren__lnc_shouhou/multi_compare")                              #设置工作目录
X <- read.delim("lnc_merge_readcount.xls",header=T,sep="\t")                                       #读入数据

X$p_value <-              #对数据进行配对t检验
apply(X[, c(2:7,8)], 1, 
    function(x) {                
        if(sd(x[c(2,4,6)])==0 | sd(x[c(1,3,5)])==0)  {
	    1 
        } else if(x[7] < 0.05) {
	    t.test(x[c(2,4,6)], x[c(1,3,5)], var.equal=FALSE,paired = TRUE)$p.value 
        } else {
	    t.test(x[c(2,4,6)], x[c(1,3,5)],paired =TRUE)$p.value
        }
    }
)
   
X$p_fdr <-p.adjust(X$p_value, method="fdr")                                                        #对检验的p值进行校正
write.table(X[1:10],"lnc_test_t.test.xls",row.names = F,quote = F,sep="\t")                        #输出检验及校正结果
```
以上为配对样本t 检验的R语言实现。
### 1.2 独立样本T检验 
独立样本t检验：将受试对象完全随机地分配到两组中，这两组分别接受不同的处理。这样的设计称为两组完全随机化设计，也叫成组设计。针对独立样本t检验，只需要在检验时选择，paired=FALSE。独立样本t 检验之前，需要对于两组独立样本进行F检验，判断两组样本之间的方差是否相等，在SPSS软件中为第一步的方差齐性检验。
``` r
X$p.F.test <-            #先进行F检验,检验二者方差是否相等
apply(X[, 12:17], 1, 
    function(x) {
        var.test(x[c(1,3,5)], x[c(2,4,6)])$p.value
    }
)
X$p_value <- 
apply(X[, c(12:17,18)], 1,
           function(x) {
           if(sd(x[c(2,4,6)])==0 | sd(x[c(1,3,5)])==0) {
             1
           } else if(x[7] < 0.05) {
           t.test(x[c(2,4,6)], x[c(1,3,5)],paired=FALSE)$p.value
           }else {
           t.test(x[c(2,4,6)], x[c(1,3,5)], var.equal=TRUE)$p.value
           }
     }
)              #paied 选择为FALSE
X$p_fdr <-p.adjust(X$p_value, method="fdr")  
write.table(X[1:20],"lnc_test_t.test.xls",row.names = F,quote = F,sep="\t") 
```
## 2 方差分析（ANOVA） 
方差分析是统计分析的一种常见的分析方法，对于老师实验设计比较复杂，比如平常农口实验设计多时间点的处理样本，我们常用的两两比较在处理这种时间序列的实验设计就感觉比较单一，不能满足老师的分析要求，因而此时可以使用的方差分析（ANOVA)完成分析。
### 2.1 one-way-ANOVA(单因素方差分析） 
还是以之前的一个项目为例 
| 0h     | 1h     | 6h      | 24h     | 7d     | 18d     | 检验方法                       |
| -- | -- | -- | -- | -- | -- | -- |
| eSR0h  | eSR1h  | eSR6h   | eSR24h  | eSR7d  | eSR18d  | one-way-anova              |
| eSR0h  | eSR1h  | eSR6h   | eSR24h  |        |         | short_term（one-way-anova)  |
| eSR0h  | eSR7d  | eSR18d  |         |        |         | long_term (one way anova)  |

  
这个项目是老师采取的一系列时间点的处理的胡杨的根组织，老师的分析要求是探究在这连续的时间序列内的显著变化的差异基因，这是一个典型的单因素的方差分析。以处理和非处理的为单一因素，探究时间序列上差异基因变化。这种检验方法使用R里面的edgeR实现起来比较容易。
针对单一因素方差分析，其实在edgeR包内也不是一般意义下的单因素anova分析，在edgeR内该分析被称作An ANOVA-like test for any diㄦences。在edgeR中，我们设计design的时候可以查看design的情况，在差异基因分析是，有一个重要的参数来coef(coefficients ),选择共同表达的组别。coef表示的时你选择的共同表达分析的比较组状况。
``` r
libary(edgeR)
libary(limma)
setwd('/TJPROJ1/RNA/project/201511/Regcon/NHM150353_huyang_lnc/aftersale/ANOVA_multicompare/1st_dataset/') #设置工作目录 
rc <- read.delim('first_dataset.xls', check.names=FALSE, stringsAsFactors=FALSE) #读入文件
group <- factor( c("control","control","control","treat1","treat1","treat1","treat2","treat2","treat2",
"treat3","treat3","treat3","treat4","treat4","treat4","treat5","treat5","treat5"),
levels <- c('control','treat1','treat2','treat3','treat4','treat5')) #设置分组及处理因素
y <- DGEList(counts=rc[, 2:19],genes=rc[, 1], group=group )  #edgeR读入数据数据
y <- calcNormFactors(y)  #标准化
design <- model.matrix(~group) #设置实验设计
rownames(design) <- colnames(y)  #导入列名称
y <- estimateGLMCommonDisp(y, design, verbose=TRUE) #计算离差
y <- estimateGLMTrendedDisp(y, design)
y <- estimateGLMTagwiseDisp(y, design)
fit <- glmFit(y, design) #线性拟合
lrt <- glmLRT(fit, coef=2:6) # 找出与实验设计变化相关的基因
ttg <- topTags( lrt, adjust.method="BH", sort.by="p.value", n=90587) #对差异分析结果进行FDR校验
write.table(ttg, file='./first_dataset_test_out.xls', sep='\t', quote=F, row.names=F, col.names=T) #导出运算结果
```

### 2.2 two-way-anova(双因素方差分析） 
以在做的一个项目为例，跟之前的单因素方差是一个项目，如下图。

|       | salt     |control |  
|--|--|--|
| root  | eSR24h  | eCR24h       |
| stem  | eSS24h  | eCS24h       |
| leaf  | eSL24h  | eCL24h       |

``` r
library(edgeR)
library(multcomp)
fpkm <- read.delim("dataset.xls", sep="\t", header=T, quote="")
treat <- factor(c(rep(c('control', 'control', 'control', 'salt', 'salt', 'salt'), 3)))
tissue <-factor(c('root','root','root','root','root','root','stem','stem','stem','stem','stem','stem','leaf','leaf','leaf','leaf','leaf','leaf'))
design < -model.matrix(~ treat+tissue) #实验设计（重要）
dge<-DGEList(counts=fpkm[, 2:19], genes=fpkm[, 1], group=rep(c('R_control','R_salt','S_control','S_salt','L_control','L_salt'),each=3))
dge <- calcNormFactors(dge)
dge <- estimateGLMCommonDisp(dge, design)
dge <- estimateGLMTagwiseDisp(dge, design)
fit <- glmFit(dge, design )
lrt.dge <- glmLRT(fit, coef=2:6 ) #设置共分析的组别（重要）
xx<- topTags(lrt.dge, adjust.method="BH", sort.by="logFC", n=90587)
write.table(xx, "two_way_anova_logfc.xls", sep="\t")
```
老师实验设计比较复杂，需要分析 在处理和非处理情况下，不同组织的基因表达差异分析，这个就包含了两对外在因素。处理和非处理，组织的不同（根，茎，叶）。要在这两个因素下进行差异分析的分析。
同时对于两个因素的处理也会有所考虑。针对双因素的方差分析,在design的设置上也有一些其他的注意事项。在本次试验中，双因素包括为两个因素：tissue 和 treat ,这两个因素之间没有交叉影响，因而在设置design的时候两因素之间使用的是“+”，但是如果两因素之间有交叉作用的话，实验设计应该使用tissue*treat。
针对与双因素方差分析的实验设计，同样查阅了《R语言实战》一书，书内对于常见研究设计有表达式，下表表达式针对于R包里普通的aov函数。
| 设计                |表达式 |
| -- | -- |
| 单因素ANOVA          | y~A          |
| 含单个协变量的单因素ANCOVA  | y~x+A        |
| 双因素ANOVA          | y~A*B        |
| 含两个协变量的双因素ANCVOA  | y~x1+x2+A*B  |

## 参考资料

生物统计学（第五版）科学出版社 李春喜、姜丽娜、邵云 等

R语言实战 （第三板）   

edgeRUsersGuide 
