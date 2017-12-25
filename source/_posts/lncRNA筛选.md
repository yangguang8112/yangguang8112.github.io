---
title: lncRNA筛选
date: 2016-11-28 11:54:08
tags: 生物信息学
---
lncRNA的筛选是lncRNA分析流程中的关键步骤
梳理一下流程中的筛选步骤,还有4种编码潜能预测软件
<!--more-->
### 链方向，exon，长度筛选
1.去除无链方向的转录本
``` bash
awk '{if ($7 != "."){print $0}}' merged.gtf > step0_stranded_merged.gtf
```
2.去除但外显子及长度筛选
``` bash
python /PUBLIC/source/RNA/lncRNA/filter/version5/exon_length_filter.py --gtf step0_stranded_merged.gtf --out_dir outdir --single_exon no/yes
#结果文件为：exonfilter:step1_exon_filter.gtf ,长度筛选：step2_length_filter.gtf；其中single_exon参数，植物选yes，动物选no；
#同时输出assembled_tr_info.json拼接转录本信息的json文件，用于后续提取转录本信息
```
### 已知注释筛选
1.去除有已知数据库注释的lncRNA（如物种无已知数据库注释，则不做该部分筛除）:
``` bash
/PUBLIC/source/RNA/lncRNA/softwares/cufflinks-2.2.1_patched//cuffcompare -T -C -r gencode_v24.gtf -R step2_length_filter.gtf -o lnc_database_compare
#-r 已知lnc数据库中的注释文件；-R 通过exon及长度筛选的转录本注释文件 -o 输出文件前缀 
python /PUBLIC/source/RNA/lncRNA/filter/version5/annotated_transcript_filter.py --input_gtf step2_length_filter.gtf --compared_gtf lnc_database_compare.combined.gtf --output_gtf tmp_step3_1_lncRNA_filter.gtf --ref_gtf gencode_v24.gtf --compare_flag lncRNA
#-input_gtf 通过exon及长度筛选的转录本注释文件 ;--compared_gtf cuffcompare输出的所有转录本的combine注释文件 ；--ref_gtf 已知lnc数据库中的注释文件 ； --compare_flag 本次筛除的注释转录本类型
```
2.去除参考基因组中的lncRNA
``` bash
/PUBLIC/source/RNA/lncRNA/softwares/cufflinks-2.2.1_patched//cuffcompare -T -C -r lncRNA_filter/ref/lncRNA.gtf -R tmp_step3_1_lncRNA_filter.gtf -o ref_lnc_compare
#-r 参考基因组中lncRNA注释文件；-R 已筛除数据库中lncRNA的转录本注释文件 -o 输出文件前缀
python /PUBLIC/source/RNA/lncRNA/filter/version5/annotated_transcript_filter.py --input_gtf tmp_step3_1_lncRNA_filter.gtf --compared_gtf ref_lnc_compare.combined.gtf --output_gtf step3_1_lncRNA_filter.gtf --ref_gtf lncRNA_filter/ref/lncRNA.gtf --compare_flag lncRNA
#-input_gtf 已筛除数据库中lncRNA的转录本注释文件 ;--compared_gtf cuffcompare输出的所有转录本的combine注释文件 ；--ref_gtf 参考基因组中lncRNA注释文件 ； --compare_flag 本次筛除的注释转录本类型
```
3.去除参考基因组已注释的其他RNA
``` bash
/PUBLIC/source/RNA/lncRNA/softwares/cufflinks-2.2.1_patched//cuffcompare -T -C -r lncRNA_filter/ref/other_ncRNA.gtf -R step3_1_lncRNA_filter.gtf -o other_ncRNA_compare
python /PUBLIC/source/RNA/lncRNA/filter/version5/annotated_transcript_filter.py --input_gtf step3_1_lncRNA_filter.gtf --compared_gtf other_ncRNA_compare.combined.gtf --output_gtf step3_2_other_ncRNA_filter.gtf --compare_flag other_ncRNA
```
4.通过merge注释的classcode进行已知mrna的注释去除
``` bash
python /PUBLIC/source/RNA/lncRNA/filter/version5/annotated_transcript_filter.py --input_gtf step3_2_other_ncRNA_filter.gtf --compared_gtf step0_stranded_merged.gtf --output_gtf step3_3_mRNA_filter.gtf --compare_flag mRNA
```
### 对所有准备用于分析的转录本进行定量
1.准备
``` bash
cat ref_mRNA_gtf step3_3_mRNA_filter_gtf annotated_lncRNA.gtf > step4_exp_test_gtf
#将现阶段所有可用的RNA注释文件cat到一起，用于后续定量; annotated_lncRNA.gtf整合了已知数据库及参考基因组中注释的已知lncRNA
```
2.单样品定量
``` bash
/PUBLIC/source/RNA/lncRNA/softwares/cufflinks-2.2.1_patched//cuffquant --library-type fr-firststrand -o outdir step4_exp_test.gtf sample1.bam -p 2
#输出文件为abundances.cxb的二进制文件
```
3.cuffnorm进行cuffquant定量结果的整合
``` bash
/PUBLIC/source/RNA/lncRNA/softwares/cufflinks-2.2.1_patched//cuffnorm --library-type fr-firststrand -o cuffnorm \
 -L sample1,samplen \
 step4_exp_test.gtf \
sample1/abundances.cxb 
samplen/abundances.cxb   --num-threads 2
#-o 定量结果输出路径（结果文件isoforms,gene,cds等的fpkm_table及count_table）,lncRNA分析中应用的isoforms.fpkm_table 
#-L  样品名列表（“,”分隔） 
python /PUBLIC/source/RNA/lncRNA/filter/version5/expression_filter.py --exp_table isoforms.fpkm_table --gtf step4_exp_test.gtf --tr_info assembled_tr_info.json --output step4_exp_filter.gtf
#多外显子过滤掉所有样品fpkm<0.5的转录本；单外显子过滤掉所有样品fpkm<2的转录本；
#输出结果：1. step4_exp_filter.gtf；2.exp_annotated_lncRNA.gtf(过滤掉低表达的已知lnc)
perl /PUBLIC/source/RNA/lncRNA/filter/version5/gtf2trbed.pl step4_exp_filter.gtf step4_exp_filter.bed   #gtf to bed
perl /PUBLIC/source/RNA/lncRNA/lncRNA_v1/public/extractTranscriptsfromFA.pl step4_exp_filter.gtf genome.fa step4_exp_filter.fa  #提取转录本序列文件
perl /PUBLIC/source/RNA/lncRNA/filter/version5/seq_split_v1.pl step4_exp_filter.fa step4_exp_filter 200 step4_exp_filter_split_fa 
#将过滤了低表达转录本的序列以200条作为分隔，输出到step4_exp_filter_split_fa目录下。结果为step4_exp_filter_1.fa，step4_exp_filter_2.fa....step4_exp_filter_n.fa
```
## 编码潜能预测软件
### PFAM
#### 分析步骤
这一步是拆分的
``` bash
perl /PUBLIC/source/RNA/lncRNA/Filter/PFAM/seq2protein.pl exp_filter_1.fa protein.fa
#将转录本序列转换为蛋白质序列
perl /PUBLIC/source/RNA/lncRNA/Filter/PFAM/pfam_scan.pl \
-fasta protein.fa \
-dir /PUBLIC/database/Common/PFAM \
-outfile pfam_scan.out \
-pfamB -cpu 2
#将蛋白质序列与pfam数据库比对,输出结果为pfam_scan.out
```
处理pfam的输出文件,得到无编码潜能的转录本id列表
``` bash
cat pfam_scan.out |sed -e '/^#/d' -e '/^$/d' |awk 'OFS="\t"{print ($1,$6,$7,$8,$9,$10,$11,$12,$13,$14,$15)}' > pfam_scan.out.temp
cat /PUBLIC/source/RNA/lncRNA/lncRNA_v1/Filter/PFAM/bin/name.xls pfam_scan.out.temp > PFAM.result.xls
python /PUBLIC/source/RNA/lncRNA/lncRNA_v1/Filter/PFAM/bin/select_pfam_id.py exp_filter.gtf /pfam_scan.out.temp PFAM.id.txt
rm pfam_scan.out.temp
```

### CPC
#### 分析步骤
这步是拆分的
``` bash
perl /PUBLIC/source/RNA/lncRNA/filter/version5/CPC/runCPC.pl -seq exp_filter_1.fa -db eu -strand plus -outdir outdir
```
``` bash
#runCPC.pl参数help信息
perl /PUBLIC/source/RNA/lncRNA/filter/version5/CPC/runCPC.pl --help
## 
Run Coding Potential Calculator program.

Options: 
	-seq	<s>:	Fasta formated sequences to be calculated.
	-db	<s>:	Protein database to search for. {"nr","eu" or "sp"};
			  nr: the whole NCBI protein database;
			  eu: the NCBI eukaryotes` protein database;
			  sp: the SwissProt database;
			  Not needed if blast result provided.
	-blast	<s>:	Blast result agaist known protein database.
	-bfmt	<s>:	Blast format. {blast, blastxml or blasttable}.
			  Corespand to 0,5,6 blast+ outfmt option.
	-strand <s>:	Which strand of sequences for coding potential analysis.
			  "both", "plus" or "minus".
	-outdir	<s>:	Output directory.
	-help	<s>:	help.
Usage:
	1. perl /PUBLIC/source/RNA/lncRNA/filter/version5/CPC/runCPC.pl -seq <..> -db <..> -strand <..> -outdir <..>
	2. perl /PUBLIC/source/RNA/lncRNA/filter/version5/CPC/runCPC.pl -seq <..> -blast <..> -bfmt <..> -outdir


## 
```
这一步的输出文件名前缀为fa的文件名,总共输出四个文件:
blast.txt
cpc.txt
evidence.homo
evidence.orf
其中cpc.txt为编码潜能预测结果

接下来同样是结果文件处理,获得非编码转录本id
``` bash
cat  /PUBLIC/source/RNA/lncRNA/lncRNA_v1/Filter/CPC/bin/name.xls cpc.txt > CPC.result.xls
awk '{if($3 == "noncoding"){print $1}}' CPC.result.xls > CPC.id.txt
```

### CNCI
#### 分析步骤
拆分
``` bash
python /PUBLIC/source/RNA/lncRNA/softwares/CNCI_package/CNCI.py -f exp_filter_1.fa -o out_dir -m ve -p 1
```
CNCI的help信息
``` bash
/PUBLIC/source/RNA/lncRNA/softwares/CNCI_package/CNCI.py -help
Usage: CNCI.py [options]

Options:
  -h, --help            show this help message and exit
  -f input files, --file=input files
                        enter your transcript (sequence or gtf)
  -o output files, --out=output files
                        assign your output file
  -p prallel numbers, --parallel=prallel numbers
                        please enter your specified speed ratio
  -m model types, --model=model types
                        please enter your specified classification model
  -g, --gtf             please enter your gtf files
  -d DIRECTORY, --directory=DIRECTORY
                        if your input file is gtf type please enter RefGenome
                        directory
```
输出文件为CNCI.index,包含是否编码的信息

merge获得结果
``` bash
python /PUBLIC/source/RNA/lncRNA/filter/version5/merge_cnci.py cnci_dir
```

### phyloCSF
#### 分析步骤
##### prepare
以染色体拆分
``` bash
python /PUBLIC/software/RNA/galaxy/galaxy-dist2/galaxy-dist/tools/maf/interval_maf_to_merged_fasta.py -d hg38 -c 1 -s 2 -e 3 -S 6 -m chr1.maf -i split_bed//chr1.bed -t user -o split_maf/extract.chr1.maf
#chr1.maf是之前准备好的maf文件
sed -e 's/hg38./hg38|/g' split_maf/extract.chr1.maf >split_maf/rename.extract.chr1.maf
awk '{print $4"\t"$1"("$6"):"$2"-"$3}' split_bed//chr1.bed > split_bed//chr1.table
perl /PUBLIC/source/RNA/lncRNA/Filter/phyloCSF/bin/pre_phylocsf.modi.pl split_bed//chr1.table hg38 split_maf/rename.extract.chr1.maf split_fa
perl /PUBLIC/source/RNA/lncRNA/filter/version5/generate_phyloCSF_v2.pl -split_fa_dir split_fa -table split_bed//chr1.table -outdir split_script/chr1/ -maf_num hg38
python /PUBLIC/source/RNA/lncRNA/filter/version5/phylocsf_split.py split_script/chr1//script/CSF.sh 200 split_script/chr1//script/split/
```
/TJPROJ1/RNA/ncRNA201704/lncRNA/C101SC16120656_ren_lnc_2/lncRNA_filter/filter/step5_phyloCSF/split_script

