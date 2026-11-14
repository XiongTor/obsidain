![|150](https://cdn.nlark.com/yuque/0/2026/png/54922767/1785113175532-d23e71ec-260e-41b1-bcda-458393ef7fc1.png)

### 1. MP5检查数据完整性，qc进行质量检测
```bash
# 检查下载下来的数据是否是完好无损的
md5sum -c md5.txt

# md5sum是Linux系统自带的，不需要安装
# md5.txt一般测序公司会直接提供在测序文件中，直接运行

# fastqc进行质量控制
fastqc -t 8 -o qcReport/ SYHQ_leaf1_L1_1.clean.rd.fq.gz SYHQ_leaf1_L1_2.clean.rd.fq.gz
```

### 2. NT库污染检测
在采样过程中，样品很可能混入人体或其它生物的DNA，这一步即检测这种外援污染的情况
通过从测序数据中抽取10000个reads与NCBI公共库进行对比来判断

学习链接：[https://www.jianshu.com/p/ef02b83589c8](https://www.jianshu.com/p/ef02b83589c8)


```bash
# -------------------------  测试数据抽取与格式转换  ----------------------------
# 从 fastq 中抽取 reads
seqtk sample -s100 SYHQ_leaf1_L1_1.clean.rd.fq.gz 10000 > reads_control_R1.fastq
seqtk sample -s100 SYHQ_leaf1_L1_2.clean.rd.fq.gz 10000 > reads_control_R2.fastq
cat reads_control_R1.fastq reads_control_R2.fastq > reads_control_10000.fastq

# -s 100 是随机种子，保证可重复
# 10000 是抽取的 reads 数量，可以根据你的需求调整
# 注意：如果抽样数大于原始文件 reads 数量，会出错
# 提取的数量100000参考[1]杨跃仙,段利武,马山俊,等.短柄草基因组调查测序[J/OL].分子植物育种,1-11[2025-11-18].https://link.cnki.net/urlid/46.1068.S.20230506.1022.008.

# 转换 fastq → fasta
seqtk seq -a reads_control_10000.fastq > reads_control_10000.fasta

# -----------------------  与现有数据库比对，判断污染率  ------------------------
# nt数据库已经下载并构建好了在`/data/genome/Elaeagnus/nt_db/`中,下载日期为2026.04.16

# 对 NCBI nt 库进行比对---也可以在NCBI上直接比对，见下文
#服务器上运行
# 下载NT库流程https://www.cnblogs.com/liujiaxin2018/p/17556496.html
#a.下载NT库
# wget https://ftp.ncbi.nlm.nih.gov/blast/db/FASTA/nt.gz
# gunzip nt.gz
#b、创建nr/nt数据库目录
# mkdir nt_db
#c、构建nr/nt数据库
# makeblastdb -in nt -dbtype nucl -title my_nt -parse_seqids -out ./nt_db/nt -logfile make_nt.log
#d.生成进行比对的测试的fasta格式数据

#与nt库比对

blastn \
-task megablast \
-query reads_control_10000.fasta \
-db /data/genome/Elaeagnus/nt_db/nt \
-out blast_nt.tsv \
-outfmt "6 qseqid sacc pident length evalue staxids stitle" \
-num_threads 20 \
-evalue 1e-5 \
-max_target_seqs 1
# -task megablast 指定比对算法模式为 `megablast`
# -outfmt "6 ..."`**：自定义结果输出格式表头
# -db /你的路径/nt_db/nt：指定目标数据库（Database）的路径及索引前缀
# -max_target_seqs 1 ：每条 read 最多只保留1条数据库匹配结果。在污染检测中必须加上此参数，否则一条高度保守的序列可能会比对上库里几百个近缘物种的记录


```


**只提取1000条reads在NCBI上直接比对现有数据库**

![](https://cdn.nlark.com/yuque/0/2025/png/54922767/1763467719507-9af3229f-9c09-40c6-a79e-012555dab94d.png)


![](https://cdn.nlark.com/yuque/0/2026/png/54922767/1770212445336-cfa5023c-d0c8-4a60-8943-6cfb609dfd7c.png)

### 3. k-mer频数统计
jellyfish是Center for Bioinformatics and Computational Biology在2011年研发的一款对DNA的k-mers计数的软件，用Hash表储存数据，能多线程运行。
[参考](https://segmentfault.com/a/1190000041995240)

```bash
#conda安装
conda install -c bioconda jellyfish 

#github安装
github：jellyfish（https://github.com/gmarcais/Jellyfish）安装

mkdir Jellyfish_21
 
#运行：一般先用jellyfish count进行k-mer计数，然后用jellyfish histo对结果进行统计，获得k-mer的频数分布直方表sample.histo

#计算k-mer频率（K-mer分别选择17 19 21）

# k-mer 切割与计数
jellyfish count -m 21 -s 10G -t 15 -o k-mer_m21_out  -C  <(zcat SYHQ_leaf1_L1_1.clean.rd.fq.gz) <(zcat SYHQ_leaf1_L1_2.clean.rd.fq.gz)

#生成k-mer频数直方表sample.histo
jellyfish histo -o k-mer_m21_out.histo k-mer_m21_10G_out

# 汇总 k-mer 全局统计量,结果用于gce的-g参数
jellyfish stats k-mer_m21_10G_out -o k-mer_m21_10G_out.stat 


# 或则你也可以直接使用下列脚本，循环跑不同的k-mer值
# ================= 1. 参数配置区 =================

R1="SYHQ_leaf1_L1_1.clean.rd.fq.gz"
R2="SYHQ_leaf1_L1_2.clean.rd.fq.gz"
KMERS=(17 19 21)          # 需要测试的 k-mer 长度列表
THREADS=16                # 每次分配的 CPU 线程数
HASH_SIZE="2G"            # 哈希表初始槽位数（2G 约占 15-20 GB 内存；内存充足可改为 10G）
OUT_DIR="jellyfish_out"   # 结果输出目录

mkdir -p "${OUT_DIR}"

# ================= 2. 循环计算不同 k-mer =================
for k in "${KMERS[@]}"; do
    echo "=========================================="
    echo "[$(date '+%F %T')] 开始计算 k = ${k} ..."
    PREFIX="${OUT_DIR}/k-mer_m${k}_out"
    # 第一步：k-mer 计数（已内部多线程，顺序执行避免内存溢出）
    jellyfish count \
        -m "${k}" \
        -s "${HASH_SIZE}" \
        -t "${THREADS}" \
        -C \
        -o "${PREFIX}" \
        <(zcat "${R1}") <(zcat "${R2}")
    # 第二、三步：并行生成频数直方表(.histo) 和全局统计量(.stat)
    jellyfish histo -h 1000000 -t "${THREADS}" -o "${PREFIX}.histo" "${PREFIX}" &
    jellyfish stats -o "${PREFIX}.stat" "${PREFIX}" &
    wait  # 等待当前 k 值的 histo 和 stats 全部完成后再进入下一轮
    echo "[$(date '+%F %T')] k = ${k} 计算完成！"
done

# ================= 3. 汇总 GCE 所需的 -g 参数 =================
echo "=========================================="
echo "各 k-mer 统计汇总（GCE 的 -g 参数请填写 Total 对应的数值）："

for k in "${KMERS[@]}"; do
    echo "--- k = ${k} ---"
    cat "${OUT_DIR}/k-mer_m${k}_out.stat"
done
```

### 4. 基因组特征评估
GCE(Genome Characteristics Estimation) 是华大基因用于基因组评估的软件，其参考文献为：[<font style="color:rgb(77, 140, 184);">Estimation of genomic characteristics by analyzing k-mer frequency in de novo genome projects</font>](http://www.researchgate.net/publication/255722390_Estimation_of_genomic_characteristics_by_analyzing_k-mer_frequency_in_de_novo_genome_projects)
GCE 软件包中主要包含 kmer_freq_hash 和 gce 两支程序。前者用于进行 kmer 的频数统计，后者在前者的结果上进行基因组大小的准确估算。

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

