### MP5检查数据完整性
```bash
# 检查下载下来的数据是否是完好无损的
md5sum -c md5.txt

# md5sum是Linux系统自带的，不需要安装
# md5.txt一般测序公司会直接提供在测序文件中，直接运行
```

##### NT库污染
学习链接：[https://www.jianshu.com/p/ef02b83589c8](https://www.jianshu.com/p/ef02b83589c8)

```bash
#路径/data/xueqin/Project/Prunus/Prunus_Geome_analyses/Prunus_zippeliana_genome/survey/3_clean_survey
#（1）从 fastq 中抽取 reads
seqtk sample -s100 Prunus_zippeliana_1.fq.gz 100000 > reads_control_R1.fastq
seqtk sample -s100 Prunus_zippeliana_2.fq.gz 100000 > reads_control_R2.fastq
cat reads_control_R1.fastq reads_control_R2.fastq > 100000reads_control.fastq
seqtk seq -a 100000reads_control.fastq > 100000reads_control.fasta
#-s 100 是随机种子，保证可重复。
#100000 是抽取的 reads 数量，可以根据你的需求调整。
#注意：如果抽样数大于原始文件 reads 数量，会出错。
#提取的数量100000参考[1]杨跃仙,段利武,马山俊,等.短柄草基因组调查测序[J/OL].分子植物育种,1-11[2025-11-18].https://link.cnki.net/urlid/46.1068.S.20230506.1022.008.

#（2）转换 fastq → fasta
seqtk seq -a 100000reads_control.fastq > 100000reads_control.fasta
#（3）对 NCBI nt 库进行比对

```

**<font style="color:rgb(204, 0, 0);background-color:rgb(255, 187, 187);">Your query(1,502,759 bases) is longer than the maximum allowed(1,000,000 bases). You may reduce your query length or use stand-alone </font>**[<font style="color:rgb(51, 102, 153);">BLAST</font>](https://blast.ncbi.nlm.nih.gov/doc/blast-help/downloadblastdata.html)**<font style="color:rgb(204, 0, 0);background-color:rgb(255, 187, 187);">, or </font>**[<font style="color:rgb(51, 102, 153);">ElasticBLAST</font>](https://blast.ncbi.nlm.nih.gov/doc/elastic-blast/)**<font style="color:rgb(204, 0, 0);background-color:rgb(255, 187, 187);">.</font>**



**<font style="color:rgb(204, 0, 0);background-color:rgb(255, 187, 187);">只提取1000</font>**

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2025/png/54922767/1763467719507-9af3229f-9c09-40c6-a79e-012555dab94d.png)

```bash
#服务器上运行
下载NT库流程https://www.cnblogs.com/liujiaxin2018/p/17556496.html
#a.下载NT库
wget https://ftp.ncbi.nlm.nih.gov/blast/db/FASTA/nt.gz
gunzip nt.gz
#b、创建nr/nt数据库目录
mkdir nt_db
#c、构建nr/nt数据库
makeblastdb -in nt -dbtype nucl -title my_nt -parse_seqids -out ./nt_db/nt -logfile make_nt.log
#d.生成进行比对的测试的fasta格式数据
#前10000条reads作为比对query，由于是压缩格式所以使用zcat,FASTQ 每条 read 占 4 行
zcat ${name}_1.clean.fq.gz|head -40000 > reads.fa
#下载好上面114G的nt库后开始比对
blastn \
-query reads.fa \
-db /data/xueqin/Prunus/genome_assembly/1Chromosome/06_polish/00_NT库/nt.gz \
-out reads.csv \
-outfmt "10 evalue length qseqid qlen qstart qend sacc slen sstart send pident nident sstrand qcovs qseq sseq sgi stitle" \
-num_threads 20 -evalue 1e-5 -max_target_seqs 1

```

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/54922767/1770212445336-cfa5023c-d0c8-4a60-8943-6cfb609dfd7c.png)

##### survey分析具体流程
<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2026/png/54922767/1785113175532-d23e71ec-260e-41b1-bcda-458393ef7fc1.png)

#### （1）k-mer频数统计
<font style="color:rgb(33, 37, 41);">jellyfish是Center for Bioinformatics and Computational Biology在2011年研发的一款对DNA的k-mers计数的软件，用Hash表储存数据，能多线程运行。</font>

```bash
#k-mer频数统计，参考https://segmentfault.com/a/1190000041995240
jellyfish（jellyfish是Center for Bioinformatics and Computational Biology在2011年研发的一款对DNA的k-mers计数的软件，用Hash表储存数据，能多线程运行）
#a.安装
#conda安装
conda install -c bioconda jellyfish 
#github安装
github：jellyfish（https://github.com/gmarcais/Jellyfish）安装
 mkdir Jellyfish_21
#运行：一般先用jellyfish count进行k-mer计数，然后用jellyfish histo对结果进行统计，获得k-mer的频数分布直方表sample.histo
#计算k-mer频率（K-mer分别选择17 19 21）
jellyfish count -m 21 -s 10G -t 15 -o Prunus_zippeliana_m21_10G_out  -C  <(zcat Prunus_zippeliana_1.fq.gz) <(zcat Prunus_zippeliana_2.fq.gz)
#生成k-mer频数直方表sample.histo
jellyfish histo -o Prunus_zippeliana_m21_10G_out.histo Prunus_zippeliana_m15_10G_out
#
jellyfish stats Prunus_zippeliana_m21_10G_out -o Prunus_zippeliana_m21_10G_out.stat #用于gce的-g参数

```

#### （2）基因组特征评估
<font style="color:rgb(51, 51, 51);">GCE(Genome Characteristics Estimation) 是华大基因用于基因组评估的软件，其参考文献为：</font>[<font style="color:rgb(77, 140, 184);">Estimation of genomic characteristics by analyzing k-mer frequency in de novo genome projects</font>](http://www.researchgate.net/publication/255722390_Estimation_of_genomic_characteristics_by_analyzing_k-mer_frequency_in_de_novo_genome_projects)<font style="color:rgb(77, 140, 184);">，</font><font style="color:rgb(51, 51, 51);">GCE 软件包中主要包含 kmer_freq_hash 和 gce 两支程序。前者用于进行 kmer 的频数统计，后者在前者的结果上进行基因组大小的准确估算。</font>

```bash
#基因组特征评估，参考https://www.plob.org/article/9388.html
#纯合
/home/xueqin/data/software/GCE-master/gce-1.0.2/gce -f Prunus_zippeliana_m21_10G_out.histo -g 48891608582 > gce.table 2> gce.log

#杂合
/home/xueqin/data/software/GCE-master/gce-1.0.2/gce -f Prunus_zippeliana_m21_10G_out.histo -g 48891608582 -H 1 -c 49 > gce2.table 2> gce2.log
#则杂合率 = kmer-species heterozygous ratio / kmer_size 。 若计算出的杂合率低于 0.2%，个人认为测序数据应该是纯合的。这时候，应该不使用 -H 1 参数。使用 -H 1 参数会对基因组的大小和重复序列含量估算造成影响
#对于不能判断纯合还是杂合的数据，可以先运行纯合模式，获得初始峰值(raw_peak)，即k-mer的期望深度，用作-c参数。
#然后再用-c参数和-H 1参数运行杂合模式。最后比较两种模式的结果，从而判断哪种更适用当前数据。
#-c int
#    kmer depth frequency 的主峰对应的 depth。gce 会在该值附近找主峰。
#-g int
#    总共的 kmer 数。一定要设定该值，否则 gce 会直接使用 -f 指定的文件计算 kmer 的总数。由于默认下该文件中最大的 depth 为 255，因此，软件自己计算的值比真实的值偏小。同时注意该值包含低覆盖度的 kmer。
#-M int
#    支持最大的 depth 值，默认为 256 。
#-m int
#   估算模型的选择，离散型（0），连续型（1）。默认为 0，对真实数据推荐选择 1 。
#-D int
#    precision of expect value，默认为 1。如果选择了 -m 1，推荐设置该值为 8。
#-H int
#    使用杂合模式（1），不使用杂合模式（0）。默认值为 0。只有明显存在杂合峰的时候，才选择该值为 1 。
#-b int
#    数据是（1）否（0）有 bias。当 K > 19时，需要设置 -b 1 。
#如果使用 -H 1 参数，则会得额外得到如下信息：

for hybrid: a[1/2]=0.223671 a1=0.49108
kmer-species heterozygous ratio is about 0.125918
 
上面结果中，0.125918 是由 a[1/2] 计算出来的。 0.125918 = a[1/2] / （ 2- a[1/2] ) 。
a[1/2]=0.223671 表示在所有的 uniqe kmer 中，有 0.223671 比例的 kmer 属于杂合 kmer 。
 
此外，有 a[1/2] 和 b[1/2] 的值在最后的统计结果中。重复序列的含量 = 1 - b[1/2] - b[1] 。
```

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2025/png/54922767/1745198810737-e9ae592f-decf-487d-b2ad-29a4d2e2591f.png)

**repeat ≈ 100% − uniq = 100 − 35.6 = 64.4%**

GenomeScope Profile

len:314,608,544bp uniq:35.6%

aa:91.5% ab:8.53%

kcov:49.5 err:0.435% dup:2.23 k:17 p:2

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2025/png/54922767/1763814598502-cf62e6b6-1644-49bd-9a59-1f1846a9de33.png)**#增加正确的图**





#### （3）基因组倍型评估
Smudgeplot从k-mer数据库中提取杂合k-mer对，然后训练杂合k-mer对。

通过比较k-mer对覆盖度的总数(CovA + CovB)和相对覆盖度(CovB / (CovA + CovB))，统计杂合k-mers对的数量，Smudgeplot可以解析基因组结构。

```bash
#参考https://zhuanlan.zhihu.com/p/529851006
conda activate smudgeplot(老服务器)
mkdir tmp
ls *.fastq.gz > FILES
## 第一步：使用KMC计算k-mer频率，生成k-mer频率表
kmc -k21 -t16 -m64 -ci1 -cs10000 @FILES kmcdb tmp #计算k-mer频率
kmc_tools transform kmcdb histogram kmcdb_k21.hist -cx10000 #生成k-mer频数直方表sample.histo和k-mer直方图
## 第二步：估计覆盖阈值上(U)下(L)限
L=$(smudgeplot.py cutoff kmcdb_k21.hist L)
U=$(smudgeplot.py cutoff kmcdb_k21.hist U)
echo $L $U # these need to be sane values
## 第三步：提取阈值范围的k-mers，计算k-mer pairs
kmc_tools transform kmcdb -ci"$L" -cx"$U" reduce kmcdb_L"$L"_U"$U"
smudge_pairs kmcdb_L"$L"_U"$U" kmcdb_L"$L"_U"$U"_coverages.tsv kmcdb_L"$L"_U"$U"_pairs.tsv > kmcdb_L"$L"_U"$U"_familysizes.tsv 
## 第四步：生成污点图(smudgeplot)
smudgeplot.py plot kmcdb_L"$L"_U"$U"_coverages.tsv
```

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2025/png/54922767/1745200012970-d76c993e-a4a9-4700-bb12-81a76ef164f7.png)

#### #k-mer和基因组的峰的值
