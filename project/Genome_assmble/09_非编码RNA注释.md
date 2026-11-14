### 9.非编码RNA注释
**统计的完整流程(学习链接：**[**https://www.jianshu.com/p/22d5222ddf71、https://wangxingbin95827.github.io/2025/01/06/%E5%9F%BA%E5%9B%A0%E7%BB%84%E6%B3%A8%E9%87%8A%E4%BB%8E0%E5%BC%80%E5%A7%8B(3)ncRNA%E6%B3%A8%E9%87%8A/)**](https://wangxingbin95827.github.io/2025/01/06/%E5%9F%BA%E5%9B%A0%E7%BB%84%E6%B3%A8%E9%87%8A%E4%BB%8E0%E5%BC%80%E5%A7%8B(3)ncRNA%E6%B3%A8%E9%87%8A/)

**原理**

**非编码RNA(Non-coding RNA, ncRNA) 包括rRNA，tRNA，snRNA，snoRNA 和microRNA 等不编码蛋白质的RNA，它们转录后直接在RNA 水平上就能行使各自的生物学功能，并不需要翻译成蛋白质**

#### **（1）tRNAscan‑SE、Barrnap、cmscan + Rfam注释非编码RNA**
```bash
#路径：

#conda activate base(老服务器)
#1. 软件准备
tRNAscan-SE：专门用于tRNA注释
官网下载：http://lowelab.ucsc.edu/tRNAscan-SE/

Infernal + Rfam数据库：用于注释其他ncRNA（miRNA、rRNA、snRNA等）
Infernal官网：http://eddylab.org/infernal/
Rfam数据库：https://rfam.xfam.org/

#2. 流程示例
Step 1: tRNA注释（tRNAscan-SE）
# 假设你有基因组序列 genome.fa
#conda activate base
#conda install bioconda::trnascan-se -y
tRNAscan-SE -o tRNA.out -f tRNA.struct -m tRNA.stats genome.fa
#结果tRNA.out包含所有tRNA预测信息

#tRNA.stats文件中包含了tRNA预测的结果统计(已经去除了低质量的)
#Infernal-confirmed tRNAs:     2375
#tRNAscan.gff3获取
perl /data/xueqin/Project/Prunus/Prunus_Geome_analyses/Prunus_zippeliana_genome/Haphic_t2t/ncRNA_2025.7.13/biocode-master/gff/convert_tRNAScanSE_to_gff3.pl -i tRNA.out >tRNAscan.gff3

Step 2: rRNA
#/data/xueqin/Project/Prunus/Prunus_Geome_analyses/Prunus_zippeliana_genome/Haphic_t2t/ncRNA_2025.7.13/rRNA.sh
/data/xueqin/software/barrnap-master/bin/barrnap --kingdom euk --threads 20 --outseq rRNA.fa T2T-Polish.fa >rRNA.gff3
#--kingdom euk:euk bac arc mito
# 细菌bacteria (5S,23S,16S)
# 古菌archaea (5S,5.8S,23S,16S)
# 多细胞生物线粒体metazoan mitochondria (12S,16S)
# 真核生物eukaryotes (5S,5.8S,28S,18S)
#rRNA.fa:rRNA结果序列
#rRNA.gff3:rRNA gff结果

#结果统计
#a.
#从 rRNA.gff3 文件统计
grep -v '^#' rRNA.gff3 | grep -w "rRNA" | wc -l
#6047

#按 rRNA 类型分类统计（如 5S、5.8S、18S、28S）
grep -v '^#' rRNA.gff3 | grep "product=" | cut -d"=" -f2 | cut -d";" -f1 | sort | uniq -c
# 1365 18S_rRNA
# 1525 28S_rRNA
# 1242 5_8S_rRNA
# 1915 5S_rRNA

#一条命令列出每种 rRNA 类型的数量
grep -v '^#' rRNA.gff3 | grep "product=" | sed 's/.*product=//;s/;.*//' | sort | uniq -c
# 1220 18S ribosomal RNA
# 145 18S ribosomal RNA (partial)
# 1198 28S ribosomal RNA
# 327 28S ribosomal RNA (partial)
# 1235 5.8S ribosomal RNA
# 7 5.8S ribosomal RNA (partial)
# 1338 5S ribosomal RNA
# 577 5S ribosomal RNA (partial)

#b.
seqkit stats T2T-Polish.fa
# file           format  type  num_seqs      sum_len  min_len      avg_len     max_len
# T2T-Polish.fa  FASTA   DNA        261  915,815,529   27,564  3,508,871.8  90,496,335
bash stats_ncRNA.sh rRNA.gff3 915815529
# Type    Copy    Average_length(bp)      Total_length(bp)        %_of_genome
# 18S_rRNA        1365    1708    2331749 .254600
# 28S_rRNA        1525    3185    4857236 .530300
# 5_8S_rRNA       1242    153     190680  .020800
# 5S_rRNA 1915    101     194050  .021100

Step 3: 其他ncRNA注释（Infernal + Rfam）
#下载Rfam的cm数据库
wget ftp://ftp.ebi.ac.uk/pub/databases/Rfam/CURRENT/Rfam.cm.gz
gunzip Rfam.cm.gz
wget ftp://ftp.ebi.ac.uk/pub/databases/Rfam/CURRENT/Rfam.clanin
# cmpress 索引
cmpress Rfam.cm
#运行Infernal搜索
bash cmscan_ncRNA.sh
#/data/xueqin/Project/Prunus/Prunus_Geome_analyses/Prunus_zippeliana_genome/Haphic_t2t/ncRNA_2025.7.13
genome_total=915815529  # 你的基因组大小（单位bp）
# 统计模型数量（假设你也使用Rfam数据库）
CMnumber=`grep "ACC" ./Rfam.cm | wc -l`
# 估算Z值
Z=`echo $genome_total*2*$CMnumber/1000000 | bc`
# 运行cmscan
cmscan -Z $Z --cut_ga --rfam --nohmmonly --tblout my_genome.tblout \
  --fmt 2 --cpu 30 --clanin ./Rfam.clanin \
  ./Rfam.cm T2T-Polish.fa > T2T-Polish.fa.ncRNA.cmscan



```

#### **<font style="color:rgb(64, 64, 64);">（2）非编码RNA(Non-coding RNA, ncRNA) 注释结果</font>**
```bash
Step 4: 统计各类sample.tblout中ncRNA总数
#整理注释结果文件sample.tblout 提取必需的列，非重叠区域或重叠区域得分高的区域
awk 'BEGIN{OFS="\t";}{if(FNR==1) print "target_name\taccession\tquery_name\tquery_start\tquery_end\tstrand\tscore\tEvalue"; if(FNR>2 && $20!="=" && $0!~/^#/) print $2,$3,$4,$10,$11,$12,$17,$18; }' my_genome.tblout >sample.tblout.xls

#下载rfam注释
#在rfam官网，选择【SEARCH】-【Entry type】
#然后选中所有的Entry types（包括Gene，Intron，Cis-regulatory element），点击【Submit】，会列出所有RNA family的注释信息。
#手动选择所有注释信息，复制，粘贴到新建的空白文本文件rfam.txt并保存。
#把rfam.txt传输到服务器，最好用dos2unix rfam.txt转换文件格式为unix版本。
拆分第三列cat rfam.txt | awk 'BEGIN {FS=OFS="\t"}{split($3,x,";");class=x[2];print $1,$2,$3,$4,class}' > rfam_anno.txt
#rfam注释文件rfam_anno.txt包含了所有rfam的类型type和功能描述description信息。

#统计ncRNA注释结果 
awk 'BEGIN{OFS=FS="\t"}ARGIND==1{a[$2]=$5;}ARGIND==2{type=a[$1]; if(type=="") type="Others"; count[type]+=1;}END{for(type in count) print type, count[type];}' rfam_anno.txt sample.tblout.xls >sample.ncRNA.statistic
#sample.ncRNA.statistic输出示例：
# riboswitch	2
# ribozyme	5
# tRNA	2344
# Others	1409
# miRNA	306
# antisense	58
# rRNA	4460
# sRNA	2
# snRNA	1951

#也可将注释结果整理为gff3文件：
perl /data/xueqin/Project/Prunus/Prunus_Geome_analyses/Prunus_zippeliana_genome/Haphic_t2t/ncRNA_2025.7.13/bioscripts-main/saved_scripts/infernal-tblout2gff.pl --cmscan --fmt2 my_genome.tblout >sample.infernal.ncRNA.gff3

Step 5: 已有的 3 条注释线路（tRNAscan‑SE、Barrnap、cmscan + Rfam）全部汇总成一张总表，并输出一张简洁统计表
bash ncRNA_summary.sh
# tRNA	2375
# 5SribosomalRNA	1915
# 18SribosomalRNA	1365
# 28SribosomalRNA	1525
# 5.8SribosomalRNA	1242
# rRNA_total	6047
# miRNA 3500
# snRNA 678
```

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2025/png/54922767/1752468724978-d8927b82-a8ea-4aac-bddb-17b959f6a945.png)<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2025/webp/54922767/1752468745897-e8a0da6c-dc2b-4b53-8c93-4fdc2d044ec0.webp)<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2025/webp/54922767/1752468829904-3cb17619-779b-46db-8e21-9d5e13a267cb.webp)

**#结果展示**

| tRNA | 2375 |
| :---: | :---: |
| 5SribosomalRNA | 1915 |
| 18SribosomalRNA | 1365 |
| 28SribosomalRNA | 1525 |
| 5.8SribosomalRNA | 1242 |
| rRNA_total | 6047 |
| miRNA | 3500 |
| snRNA | 678 |


