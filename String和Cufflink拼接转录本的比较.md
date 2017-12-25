StringTie应用网络流算法以及可选的从头组装来拼接转录本。在仿真数据集和真实数据集的分析结果中，相比于Cufflinks等方法，StringTie具有以下优势

（1）拼接出更完整的转录本，

（2）拼接处更准确的转录本，

（3）更好的估计转录本的表达水平，

（4）拼接速度更快

### StringTie流程 


{{:知识:stringtie.png?300|}}


**Step1**: （可选的）将reads组装成"Super-reads"。如果两个reads间有k-mer的overlap就进行延伸，指导两个方向都不再能延伸位置，最后组装成更长的序列，被称之为"Super-reads"。

**Step2**：将readsmapping到参考基因组。**StringTie+SR**方法使用的是混合reads，即super-reads和unassembled-reads，而**StringTie**使用的是测序得到的所有reads。

**Step3**: 经reads mapping，将reads聚集成cluster，对产生的每个cluster构建相应的可变剪切图。每个可变剪切代表基因所有可能的转录本异构体。

**Step4**：从可变剪切图中识别有最高reads覆盖的路径，对该路径构建流网络。

**Step5**：利用网络流算法来分配reads给转录本，使转录本覆盖的reads数目最多。

**Step6**：去除剪切图中在Step5步骤使用过的reads，迭代进行Step4-Step5，直到没有路径可循。






### 使用数据集 

**（1）仿真数据集两套**：
数据来源：UCSC Genome Browser 
仿真方法：Flux Simulator
reads长度：150 million 75-bp paired-end reads

Sim-I: the fragment sizes follow an empirical distribution based on Illumina sequences,
Sim-II :the fragment sizes follow a parameterized normal distribution

**（2）真实数据集4套**，其中3套链特异，1套链非特异的
数据来源：ENCODE

a. 链特异数据集： 
Blood ：90 million 76-bp paired-end reads
Lung : 145 million 101-bp pairedend reads
Monocytes(单核细胞): 120 million 76-bp paired-end reads

b. 链非特异数据集：
nuclear RNA from a human kidney cell line(自己测)：~180 million 100-bp paired-end reads with an average fragment length of 177 bp\


### 结果比较 

**1.性能比较：拼接转录本**

{{:知识:性能比较1.png?600|}}

当考虑有reads部分覆盖（丰度低）和完全覆盖（丰度高）的转录本时，

与Cufflinks等方法相比，StringTie和StringTie+SR具有更高的灵敏度和更低的假阳性（1-精确度）

与StringTie方法相比，StringTie+SR的灵敏度和精确度均有稍微的提升，主要是因为StringTie+SR方法预先将reads拼接成更长的super-reads

{{:知识:性能比较2.png?600|}}

当考虑有reads完全覆盖（高丰度）的转录本时，

与Cufflinks等方法相比，StringTie和StringTie+SR具有更高的灵敏度和更低的假阳性

与StringTie方法相比，StringTie+SR的灵敏度和精确度均有稍微的提升

**2.性能比较：计算转录本表达**


{{:知识:性能比较3.png?600|}}

利用斯皮尔曼等级相关来计算识别转录本与真实转录本表达的相关性来评价转录本定量的准确性

在Sim-I和Sim-II数据集中，StringTie和StringTie+SR计算出的相关性均要高于Cufflinks等方法，说明这两种方法对转录本的定量要比Cufflinks方法更为准确

**3.识别转录本数量比较**

{{:知识:性能比较4.png?600|}}

在四套真实数据集中，StringTie和StringTie+SR这两种方法要比Cufflinks等方法识别出更多数量的转录本

**4.计算的消耗**

|            | StringTie+SR   | StringTie   | Cufflinks   | Traph   | Scripture   | IsoLasso   |
|--|--|--|--|--|--|--|
^ Sim-I      | 13             ^ 17          ^ 91          ^ 2885    ^ 1526        ^ 164        ^
| Sim-II     | 11             | 23          | 81          | 1515    | 1551        | 107        |
| Kidney     | 39             | 76          | 941         | n/a     | 4765        | 286        |
| Blood      | 54             | 59          | 507         | n/a     | 2377        | 305        |
| Lung       | 59             | 53          | 1125        | n/a     | 3159        | 326        |
| Monocytes  | 33             | 35          | 411         | n/a     | 2874        | 384        |

（1）计算时间消耗：StringTie和StringTie+SR在6套数据集中转录本拼接时间均不足1h，而Cufflinks方法至少1.5h，最多将近20h

^             ^  StringTie+SR   | StringTie   | Cufflinks   | IsoLasso   |
|--|--|--|--|--|
| Sim-I       | 18.8            | 23.2        | 400.8       | 165.5      |
| Sim-II      | 15.3            | 36.5        | 368.8       | 108.1      |
| Kidney      | 48.2            | 95.1        | 2369.6      | 298.5      |
| Blood       | 125             | 130.1       | 2166.5      | 305.2      |
| Lung        | 142.5           | 135.8       | 4869        | 326.1      |
| Monocytes   | 92.3            | 93.6        | 2491.7      | 383.9      |

（2）CPU占用时间消耗：在6套数据集中，相比于Cufflinks等方法，StringTie和StringTie+SR方法均占用更少的CPU
^             ^  StringTie+SR   | StringTie   | Cufflinks   | Scripture   | IsoLasso   |
|--|--|--|--|--|--|
| Kidney      | 11.7Gb          | 12Gb        | 26.6Gb      | 24.4Gb      | 20.4Gb     |
| Blood       | 4.7Gb           | 4.3Gb       | 6.4Gb       | 16Gb        | 13Gb       |
| Lung        | 6.9Gb           | 6.4Gb       | 7Gb         | 22.2Gb      | 8.8Gb      |
| Monocytes   | 1.6Gb           | 1.8Gb       | 6.6Gb       | 17.7Gb      | 13.2Gb     |

（3）最大内存消耗：真实数据集中，StringTie和StringTie+SR最多消耗12G左右，而Cufflinks消耗多达26G

综合以上消耗，拼接转录本时，在计算时间、CPU占有时间以及最大内存消耗方面，StringTie和StringTie+SR相比于Cufflinks具有更少的消耗

### 测试结果比较 


使用的是〖P2016080179〗浙医二院6个人lncRNA测序及分析软件RNAseq-Homosapiens-TY的开发项目经Tophat比对后产生的bam文件

**1. 计算消耗的比较**


^ Sample  ^ Bam    ^ cpu                 ^^ Mem(GBs)             ^^ io             ^^ vmem                 ^^ maxvmem              ^^
|         |        | STie     | Cuff      | STie      | Cuff      | STie   | Cuff   | STie       | Cuff     | STie       | Cuff     |
|--|--|--|--|--|--|--|--|--|--|--|--|
| H1      | 4.7G   | 0:09:57  | 10:01:16  | 278.41    | 56471.39  | 5.11   | 21.53  | 570.449M   | 1.506G   | 947.520M   | 3.396G   |
| H2      | 5.2G   | 0:14:58  | 11:31:45  | 410.93    | 77337.35  | 5.50   | 30.23  | 588.285M   | 1.841G   | 716.078M   | 3.240G   |
| H3      | 5.5G   | 0:15:09  | 12:02:42  | 427.41    | 78301.86  | 5.92   | 29.15  | 575.176M   | 1.803G   | 740.000M   | 3.744G   |
| N1      | 4.7G   | 0:10:05  | 9:45:38   | 278.36    | 52799.4   | 5.05   | 23.25  | 613.043M   | 1.929G   | 896.539M   | 2.937G   |
| N2      | 5.9G   | 0:09:47  | 11:21:45  | 269.17    | 109620.8  | 4.99   | 27.51  | 572.027M   | 2.682G   | 748.594M   | 4.227G   |
| N3      | 5.8G   | 0:10:23  | 11:10:59  | 308.73    | 85997.38  | 6.18   | 24.13  | 563.809M   | 2.196G   | 1.157G     | 3.636G   |
| H1      | 4.7G   | 0:09:57  | 10:01:16  | 278.41    | 56471.39  | 5.11   | 21.53  | 570.449M   | 1.506G   | 947.520M   | 3.396G   |

平均消耗：

|            | CPU       ^ Mem      | io      | Vmem   | Maxvmem  |
|--|--|--|--|--|--|
| StringTie  | 11.49min  ^ 328.83G  ^ 5.5G    ^ 580M   ^ 846M     ^
| Cufflinks  ^ 10.85h    | 76754G   | 25.83G  | 1.95G  | 3.5G     |

由此可发现，StringTie拼接转录本消耗要比Cufflinks消耗的更少

**2. 拼接转录本数量的比较**

^ Sample   ^ Size   | StringTie   | Cufflinks   |
|--|--|--|--|
| H1       | 4.7G   | 86735       | 208518      |
| H2       | 5.2G   | 155010      | 249061      |
| H3       | 5.5G   | 132406      | 227071      |
| N1       | 4.7G   | 95246       | 209199      |
| N2       | 5.9G   | 117743      | 215553      |
| N3       | 5.8G   | 93459       | 208438      |
| Merge    |        | 204852      | 306562      |

测试StringTie拼接出来的转录本数量远少于Cufflinks拼接的数量，后续还要对两种方法拼接转录本的准确性进行验证。

**3. 拼接转录本长度比较**

{{:知识:长度.jpg?600|}}


### 使用的脚本 

1. StringTie

``` bash

stringtie /PROJ/RNA/fengli/StringTie_Cufflinks_Test/TestBam/H1/accepted_hits.bam -G /PROJ/RNA/fengli/StringTie_Cufflinks_Test/TestBam/Homo_sapiens.GRCh38.84.lnc.exon.gtf -B -o /PROJ/RNA/fengli/StringTie_Cufflinks_Test/StringTie/H1/H1_transcripts.gtf

stringtie --merge /PROJ/RNA/fengli/StringTie_Cufflinks_Test/StringTie/merge/merge.list -p 8 -G /PROJ/RNA/fengli/StringTie_Cufflinks_Test/TestBam/Homo_sapiens.GRCh38.84.lnc.exon.gtf -o /PROJ/RNA/fengli/StringTie_Cufflinks_Test/StringTie/merge/stringtie_merge.gtf -B -t
```


2. Cufflinks

``` bash

/PUBLIC/source/RNA/lncRNA/softwares/cufflinks-2.2.1_patched/cufflinks -g /PROJ/RNA/fengli/StringTie_Cufflinks_Test/TestBam/Homo_sapiens.GRCh38.84.lnc.exon.gtf -u --library-type fr-firststrand -o /PROJ/RNA/fengli/StringTie_Cufflinks_Test/Cufflinks/N1 /PROJ/RNA/fengli/StringTie_Cufflinks_Test/TestBam/N1/accepted_hits.bam

/PUBLIC/source/RNA/lncRNA/softwares/cufflinks-2.2.1_patched//cuffmerge -o /PROJ/RNA/fengli/StringTie_Cufflinks_Test/Cufflinks/merge -p 8 -g /PROJ/RNA/fengli/StringTie_Cufflinks_Test/TestBam/Homo_sapiens.GRCh38.84.lnc.exon.gtf -s /BJPROJ/RNA/reference_data/Animal/Homo_sapiens/GRCh38_Ensemble_84/lnc/Homo_sapiens.GRCh38.dna.primary_assembly.fa /PROJ/RNA/fengli/StringTie_Cufflinks_Test/Cufflinks/merge/merge.list

```


3. 统计结果

``` r

 StatInfor <- function(path)
 {
	setwd(path)                                          
	options(StringsAsFactors=FALSE)
	f = list.files()
	s = setdiff(grep('_assembly.sh.',f),grep('.png',f))
	a = sapply(f[s],function(x){read.delim2(x,header=FALSE,sep="\t")[[2]]})
	temp <- c()
	for(i in 1:length(s)){
		b = a[[i]][length(a[[i]])]
		u = unlist(strsplit(unlist(strsplit(as.character(b),","))," "))
		z = c(substr(f[s][i],1,2),
			gsub("cpu=","",u[22]),
			gsub("mem=","",u[24]),
			gsub("io=","",u[27]),
			gsub("vmem=","",u[29]),
			gsub("maxvmem=","",u[31])
			)
	temp <- rbind(temp,z)
	 }
    return(temp)
}

args<-commandArgs(T)
source("statFun.R")
STie = StatInfor(args[1])
Cuff = StatInfor(args[2])
Stat = data.frame(STie[,1:2],Cuff[,2],STie[,3],Cuff[,3],STie[,4],Cuff[,4],STie[,5],Cuff[,5],STie[,6],Cuff[,6])
setwd('/PROJ/RNA/fengli/StringTie_Cufflinks_Test')
colnames(Stat) <- c("Sample",paste(c("STie","Cuff"),rep(c("cpu","mem","io","vmem","maxvmem"),each=2),sep="_"))
write.table(Stat,"Stat.txt",col.names=T,quote=F,sep="\t",row.names=FALSE)
``` 

