---
title: PCA
date: 2016-12-08 20:29:11
tags: 生物信息学
---
主成份分析 (Principal Component Analysis, PCA) 是一种[元分析技术](https://en.wikipedia.org/wiki/Multivariate_statistics) (其它常用的还有回归分析、聚类分析、因子分析等等), 由Karl Pearson于1901年发明。PCA的核心思想在于，在尽可能保留数据的差异的前题下，降低数据的维度，也就是抽象出更少的互不相关的变量来描述各数据。可以想象，数据集是一群在多维空间中的点，在保持这一群点的相对空间位置不变的情况下，旋转到一个新的坐标系（坐标轴就是各PC），使得各点在新的坐标轴上的坐标（投影）的方差最大，而投影方差最大的坐标轴即为PC1，其次为PC2，……，此可以通过此[在线的演示](http://setosa.io/ev/principal-component-analysis/)增加理解。主成份分析可通过求取协方差矩阵的本征向量实现，本征值最大的本征向量即为PC1, 比如R的princomp函数采样的就是这种方法；也可通过奇异值分解（singular value decomposition，SVD）来实现，比如R的prcomp函数。

<!--more-->

#### 怎么做主成份分析 
首先让我们来产生一组示例数据, 相当于20个样本，各250个基因的表达值。每列是一个样本，每行是一个基因。每50行的基因表达的均值和方差不同，即是分成5簇。
``` r
DATA <- matrix(data = c(rnorm(10^3, mean = 1, sd = 1),   #这里可以改变sd看看最后图的效果
                        rnorm(10^3, mean = 2, sd = 2), 
                        rnorm(10^3, mean = 3, sd = 3), 
                        rnorm(10^3, mean = 4, sd = 4), 
                        rnorm(10^3, mean = 5, sd = 5)), 
                nrow = 250, 
                ncol = 20, 
                byrow = T)                               # 如果这里是byrow=F，则可将样本分成5簇

rownames(DATA) <- sprintf("%s%d", rep(letters[1:5], each=50), rep(1:50, times=5))
colnames(DATA) <- sprintf("%s%d", rep(LETTERS[1:5], each=4), rep(1:4, times=5))

```

我们这里描述基因之间差异，也就是将样本看成自变量。使用R的prcomp函数
``` r
pca <- prcomp(DATA, scale=T)  
names(pca)
[1] "sdev"     "rotation" "center"   "scale"    "x" 
```
得到的结果为list，含有5个元素：pca$sdev 为各主成份的标准差，其平方为方差,即本征值，可用于碎石图及计算解释方差的百分比；pca$rotation是本征向量(eigenvectors)矩阵；pca$center 为对数据进行centering所减去的值，也就是列的均值； pca$scale是进行scaling所除以的值，也就是标准差，当scale=F的时候则无这项；pca$x PCs的值，也就是原数据在个PC上的投影。平时的作图用到的是x和sdev。

可以通过一些有用的函数来查看
``` r
summary(pca)  
plot(pca)     # screeplot(pca)  #碎石图
```

快速查看各主成份作图
``` r
PCs <- data.frame(pca$x)
PCs$colours <- rep(brewer.pal(5, "Set1"), each=50)
pairs(PCs[,1:5], col=PCs$colours)
```


可以通过ggplot2作比较漂亮的图
``` r
library(ggplot2)
library(RColorBrewer)
PCs <- data.frame(pca$x)
PCs$cluster <- substr(rownames(pca$x),1,1)
ggplot() +
    geom_point(data=PCs, aes(x=PC1, y=PC2, colour=cluster)) +
    xlab(sprintf("PC1: %.2f%%", (100*(pca$sdev)^2)[1]/sum((pca$sdev)^2))) +
    ylab(sprintf("PC2: %.2f%%", (100*(pca$sdev)^2)[2]/sum((pca$sdev)^2)))
    
```

只需要将表达矩阵转置之后用于prcomp函数，即可针对样本进行PCA分析，也就是把基因看作自变量，每个样本基因数那么多维空间中的一个点，可尝试如下两段代码，看看效果
``` r
library(ggplot2)
library(RColorBrewer)

DATA <- matrix(data = c(rnorm(10^3, mean = 1, sd = 1),   
                        rnorm(10^3, mean = 2, sd = 2), 
                        rnorm(10^3, mean = 3, sd = 3), 
                        rnorm(10^3, mean = 4, sd = 4), 
                        rnorm(10^3, mean = 5, sd = 5)), 
                nrow = 250, 
                ncol = 20, 
                byrow = T)                              
rownames(DATA) <- sprintf("%s%d", rep(letters[1:5], each=50), rep(1:50, times=5))
colnames(DATA) <- sprintf("%s%d", rep(LETTERS[1:5], each=4), rep(1:4, times=5))

pca <- prcomp(t(DATA), scale=T)  

PCs <- data.frame(pca$x)
PCs$cluster <- substr(rownames(pca$x),1,1)
ggplot() +
    geom_point(data=PCs, aes(x=PC1, y=PC2, colour=cluster)) +
    xlab(sprintf("PC1: %.2f%%", (100*(pca$sdev)^2)[1]/sum((pca$sdev)^2))) +
    ylab(sprintf("PC2: %.2f%%", (100*(pca$sdev)^2)[2]/sum((pca$sdev)^2)))


DATA <- matrix(data = c(rnorm(10^3, mean = 1, sd = 1),   
                        rnorm(10^3, mean = 2, sd = 2), 
                        rnorm(10^3, mean = 3, sd = 3), 
                        rnorm(10^3, mean = 4, sd = 4), 
                        rnorm(10^3, mean = 5, sd = 5)), 
                nrow = 250, 
                ncol = 20, 
                byrow = F)                               # byrow=F，将样本分成5簇

rownames(DATA) <- sprintf("%s%d", rep(letters[1:5], each=50), rep(1:50, times=5))
colnames(DATA) <- sprintf("%s%d", rep(LETTERS[1:5], each=4), rep(1:4, times=5))

pca <- prcomp(t(DATA), scale=T)  

PCs <- data.frame(pca$x)
PCs$cluster <- substr(rownames(pca$x),1,1)
ggplot() +
    geom_point(data=PCs, aes(x=PC1, y=PC2, colour=cluster)) +
    xlab(sprintf("PC1: %.2f%%", (100*(pca$sdev)^2)[1]/sum((pca$sdev)^2))) +
    ylab(sprintf("PC2: %.2f%%", (100*(pca$sdev)^2)[2]/sum((pca$sdev)^2)))

```

#### 保留主成份的问题 
