---
title: 分bin累计算法
date: 2017-02-27 14:48:11
---
经常在一些文章中看到如下这样的图：
<img src="\img\guo_2014_nature13544_fig1a.png">
这个图里，显然是把每个基因相应的部分都分成了相同的份数，也就是分成了一个个bin，在统计每个bin里所有基因的甲基化位点数的总和，然后再计算成甲基化水平。
<!--more-->
### bin 归属的确定
这个图问题就在于怎么算出每个bin里的甲基化位点的数目，而关键就在于怎么确定每个位点属于哪个bin。这个我所知道的有两种算法：
第一种算法是先将每个区域分成bin，每个bin都对应一个坐标，标记每个bin的信息，然后再将甲基化位点坐标与这些bin的坐标做intersect，自然就确定了位点属于哪个bin。 这种很直观，大多数人很容易就能想到，但是耗资源和耗时会比较多。
第二种是，先把甲基化位点和区域做intersect，通过计算来确定位点属于哪个bin。
这里只讲这第二种算法，其它的略过，比如使用bedtools做intersect等等的就不累述了。我们现在有已经intersect好了的实例文件如下：
``` bash
mc_chr	mc_start	mc_end	region_chr	region_start	region_end	gene_id	gene_symbol	strand	region_type	overlap
1	289751	289752	1	289576	289779	ENSSSCG00000027726	DLL1	+	Stop	1
1	2668122	2668123	1	2667985	2668273	ENSSSCG00000004017	FRMD1	+	CDS	1
1	2668123	2668124	1	2667985	2668273	ENSSSCG00000004017	FRMD1	+	CDS	1
1	2670623	2670624	1	2670560	2670940	ENSSSCG00000004017	FRMD1	+	Stop	1
1	2670636	2670637	1	2670560	2670940	ENSSSCG00000004017	FRMD1	+	Stop	1
1	2670647	2670648	1	2670560	2670940	ENSSSCG00000004017	FRMD1	+	Stop	1
1	2670654	2670655	1	2670560	2670940	ENSSSCG00000004017	FRMD1	+	Stop	1
1	2670657	2670658	1	2670560	2670940	ENSSSCG00000004017	FRMD1	+	Stop	1
1	2671068	2671069	1	2670940	2672308	ENSSSCG00000004017	FRMD1	+	utr_3	1
1	2671322	2671323	1	2670940	2672308	ENSSSCG00000004017	FRMD1	+	utr_3	1
1	2671330	2671331	1	2670940	2672308	ENSSSCG00000004017	FRMD1	+	utr_3	1
1	2671332	2671333	1	2670940	2672308	ENSSSCG00000004017	FRMD1	+	utr_3	1
1	2671334	2671335	1	2670940	2672308	ENSSSCG00000004017	FRMD1	+	utr_3	1
1	2671335	2671336	1	2670940	2672308	ENSSSCG00000004017	FRMD1	+	utr_3	1
1	2671340	2671341	1	2670940	2672308	ENSSSCG00000004017	FRMD1	+	utr_3	1
1	2671348	2671349	1	2670940	2672308	ENSSSCG00000004017	FRMD1	+	utr_3	1
1	2671352	2671353	1	2670940	2672308	ENSSSCG00000004017	FRMD1	+	utr_3	1
1	2671361	2671362	1	2670940	2672308	ENSSSCG00000004017	FRMD1	+	utr_3	1
```
这时候计算bin就很容易，算出mC相对于region起点的距离，这个距离就可以算出在哪个bin，R实现如下：
``` bash
# 假设上述文件读入数据库X, 以分成100个bin为例
X$bin <- (100*(X$mc_end - X$region_start))%/%(X$region_end - X$region_start) + 1
# 这里要考虑的问题就是落在region最后一个位点的算出来是在第101个bin，矫正一下即可
if(sum(X$bin == 101) > 0)  X[X$bin == 101, ]$bin  <- 100
```
#### 这里优化一下，在算bin的时候，先减1之后再整除，就可以避免最后一个额外的bin的问题，即
``` bash
# 假设上述文件读入数据库X, 以分成100个bin为例
X$bin <- (100*(X$mc_end - X$region_start - 1))%/%(X$region_end - X$region_start) + 1
```
### 负链处理
显然上边的算法只适用于正链上的，负链需要改一下代码，如果不改代码，那么可以对坐标取相对0点的镜像。做镜像之后，基因的方向就一致了。
``` bash
mc_chr	mc_start	mc_end	region_chr	region_start	region_end	gene_id	gene_symbol	strand	region_type	overlap
1	1069999	1070000	1	1068213	1070170	ENSSSCG00000004010	ERMARD	-	utr_3	1
1	1070003	1070004	1	1068213	1070170	ENSSSCG00000004010	ERMARD	-	utr_3	1
1	1070011	1070012	1	1068213	1070170	ENSSSCG00000004010	ERMARD	-	utr_3	1
1	1070015	1070016	1	1068213	1070170	ENSSSCG00000004010	ERMARD	-	utr_3	1
1	1070034	1070035	1	1068213	1070170	ENSSSCG00000004010	ERMARD	-	utr_3	1
1	1070040	1070041	1	1068213	1070170	ENSSSCG00000004010	ERMARD	-	utr_3	1
1	1070050	1070051	1	1068213	1070170	ENSSSCG00000004010	ERMARD	-	utr_3	1
1	1070058	1070059	1	1068213	1070170	ENSSSCG00000004010	ERMARD	-	utr_3	1
1	1070339	1070340	1	1070170	1070548	ENSSSCG00000004010	ERMARD	-	Stop	1
1	1070340	1070341	1	1070170	1070548	ENSSSCG00000004010	ERMARD	-	Stop	1
1	1070341	1070342	1	1070170	1070548	ENSSSCG00000004010	ERMARD	-	Stop	1
1	1070342	1070343	1	1070170	1070548	ENSSSCG00000004010	ERMARD	-	Stop	1
```
R实现，代码如下
``` R
X[X$strand == "-", c("mc_start", "mc_end", "region_start", "region_end")] <- -X[X$strand == "-", c("mc_end", "mc_start", "region_end", "region_start")] 
```
这样，预先作这样的处理，计算bin的代码就通用了。
