---
title: CALL SNP流程记录
date: 2026-04-23
authors: Tao Xiong
tags:
  - snp
---
# 说明
蔷薇科核质冲突研究
用于Dsuite检测中滑动窗口定位渐渗区段
需要通过已有的**全基因组参考序列**为参考，进行snp calling

# 限制条件
- 在科水平的snp calling中，为了确保参考序列物种的公正性，尽量不要选取需要call snp的物种的全基因组为参考
- 科水平跨度较大，对于亲缘关系过远的物种，可以考虑选取多个参考，然后分不同的组别进行snp calling（单次call snp只能有一个参考基因组，所以说分为不同的组，每组选取一个亲缘关系最近的物种为参考）。同时需要注意，这样会导致后续的SNP难以直接合并汇总，可能需要其它方法？所以如果后续需求计算$F_{ST}$,$D_{XY}$等需要慎重考虑
- 需要考虑染色体基数，如果物种彼此间的基数不同，可能在bwa阶段就难以进行

# 参考流程
[菱角](https://app.yinxiang.com/fx/54e06bd9-9ff1-4eab-b4f9-c914923c2e1c)
[GATK官方文档教程](https://gatk.broadinstitute.org/hc/en-us/articles/360035531112--How-to-Filter-variants-either-with-VQSR-or-by-hard-filtering)
[简书](https://www.jianshu.com/p/f9c948a6c7de)
其它参考资料见本地
# 数据准备
**参考**：最好是染色体级别全基因组序列，蔷薇科GDR网站中的数据示例，[Argentina anserina](https://www.rosaceae.org/Analysis/24757635)
**准备call snp的物种**：WGS重测序数据即可，尽量选取数据质量较优的（.fq结尾，需要进行质量控制和清洗）

# 软件安装
```bash
# 创建新环境
conda create -n snp

# 安装相关包，已有可以不用安装
conda install bioconda::bwa
mamba install -c bioconda -c conda-forge gatk4 #使用mamba安装快一些
conda install bioconda::bcftools
conda install bioconda::sambamba
mamba install bioconda::plink

```

# 开始分析

## 1. BWA建立基因组索引 (index)
```bash
bwa index Fragaria_nilgerrensis.fasta -p Fragaria_nilgerrensis
# BWA是将测序数据比对到参考基因组的工具，包含BWA-backtrack, BWA-SW和**BWA-MEM**后者最新，适合70-1Mbp的长序列，最快最准
# 可以新增参数 -a 用于指定建立索引的算法,如果不加，bwa会自动选取最佳算法
# 参数-p str 输出前缀（-a str BWT索引构建算法 （is和bwtsw））
# 耗时：基因组大小：419M Real time: 484.811 sec; CPU: 482.100 sec
```

## 2. BWA循环序列比对以及格式转换
```bash

tt=$(cat wgs_srr_outgroup3.txt)
# wgs_srr.txt
# /data/xiongtao/project/Rosaceae/Rosaceae_cytonuclear/seqdata/trimmomatic/ERR14125374
# /data/xiongtao/project/Rosaceae/Rosaceae_cytonuclear/seqdata/trimmomatic/ERR12321225
# /data/xiongtao/project/Rosaceae/Rosaceae_cytonuclear/seqdata/trimmomatic/SRR15237912
for i in $tt; do 
  name=$(basename $i) 
  bwa mem -t 20 -M -R "@RG\tID:${name}\tSM:${name}" Malus_domestica ${i}_1.fq.gz ${i}_2.fq.gz 2>${name}_map.log|samtools view -b -@ 30 -|samtools sort -m 4g -@ 30 - > ${name}.srt.bam
done
	
# bwa men是比对命令，
# -R 是给每个文件加head
# -M 将次优比对标记为supplementary（兼容Picard等工具，**GATK流程推荐加**）  来源claude解释
# @RG 
#├── ID:sample # Read Group ID（样本名） 
#├── LB:sample # 文库名 
#├── SM:sample # 样本名（GATK等工具识别的关键字段） 
#└── PL:ILLUMINA # 测序平台
# Argentina_anserina_GDR.fasta是参考基因组
# -t 是线程，后面跟参考序列路径和R1R2两个文件，之间需要空格，
# 管道符“|” 后是将前面生成的sam文件转化为bam文件

# 2>${name}_map.log  记录每个样本的 bwa 日志

# samtools
# `-b`输出为 BAM 格式（输入是SAM）
# `-@ 30`使用 30 个额外线程进行压缩
# `-m 10g`每线程最大使用 10GB 内存，共约 300GB，**请确认服务器内存足够**
# `-@ 30`使用 30 个线程排序

# 运行时间 ：1h 20线程 3.7G数据量
```

## 3. Samtools统计mapping率
```bash
tt=$(cat wgs_srr_outgroup3.txt)

for i in $tt; do 
  name=$(basename $i) 
  #给bam文件建立索引 
  # samtools index ./${name}.srt.bam 
  #利用samtools的bedcov命令统计各条染色体上的reads mapping结果 
  # samtools bedcov trapa_48chr_size.bed ../Z2030_srt.bam > Z2030_48chr_readconunts.txt 
  # 接着查看mapping率 
  samtools flagstat ${name}.srt.bam > mapping/${name}_flagstat
  # 汇总
  mapping=$(cat mapping/${name}_flagstat | sed -n "5,1p" | cut -d"(" -f 2 | awk '{print $1}')
  prop_mapping=$(cat mapping/${name}_flagstat | sed -n "9,1p" | awk -F '[(]' '{print $2}' |awk '{print $1}')
  echo -e "$name $prop_mapping/$mapping" >> mapping/total_outgroup3_result.txt
done

sed -i '1i species properly_paired/mapped' mapping/total_outgroup3_result.txt

# mappint结果解读：
#注释：  
# total：分析的总reads数（bam文件所有行数）
# mapped：比对上的reads数（总体比对率）
# paired in sequencing：成对的reads总数
# read1：属于reads1的reads数量
# read2：属于reads2的reads数量
# properly paired：正确配对的reads数量
# with itself and mate mapped：一对reads均比对上的reads数
# singletons：只有单条reads比对上的reads数
# 以上计数均以reads条数计，一对reads计为两条
```

## 4. Samtools进行简单的过滤

1. 利用samtools进行简单的过滤，主要去除unmapped和mate unmapped的reads
2. 去除PCR的重复，PCR扩增数多，出现扩增错误，影响SNP识别置信度。此步骤目前有很多软件可以实现，主流应该还是使用`picard`或者`sambamba`，`picard`在此前似乎一直是惯用方法，但无法多线程运行。`sambamba`相对较新，且可以使用多线程，速度较快，本次使用`sambamba`进行分析。
3. 注意使用下列的sambamba命令后会同时生成bam文件的索引`.bam.bai`，但是当你使用其它方式进行清理的话，可能不会自动生成，还需要后续加入新的命名来生成重测序文件的bam索引
参考：
[简书比较sambamba和picard](https://www.jianshu.com/p/e20a3b73dcd0)
[sambamba_github](https://github.com/biod/sambamba)


```bash
tt=$(cat wgs_outgroup3.txt)

for i in $tt; do 
  name=$(basename $i) 
  samtools view -q 20 -f 0x0002 -F 0X0004 -F 0X0008 -b ${name}.srt.bam >${name}.srt_flt.bam  2>>log/samtools_view_log.txt  &
  sambamba markdup  -t 4  -r  -p  --tmpdir=./tmp/ ${name}.srt_flt.bam  ${name}.srt_flt.markdup.bam  2>>log/sambamba_markdup_log.txt  &
done
# 注意会自动挂在后台
# 去除unmapped耗时 28min 三个物种
# 去除pcr重复，耗时 10min 三个物种
# 总计约38 min可全部结束

# 去除PCR重复
# sambamba-markdup  [options]  <input.bam>  [<input2.bam> [...]]  <output.bam>
# 输入 BAM 必须是排序后的文件，否则报错
# 如果出现Too many open files报错，需要通过使用ulimit -n 8000或添加--overflow-list-size=600000来解决

# -r / --remove-duplicates直接删除重复reads；不加此参数则只标记不删除
# -t / --nthreads使用线程数，支持真正多线程，这是相比picard最大优势
# -l / --compression-level输出BAM压缩级别 0-9，0最快不压缩，9最慢压缩率最高，默认6
# -p / --show-progress在终端显示进度条，方便监控
# --tmpdir临时文件目录，默认 /tmp，建议指定到有大空间的目录
# --hash-table-size262144哈希表大小，用于配对read；建议设为 覆盖度 × 插入片段长度，数值越大越快但越占内存
# --overflow-list-size200000溢出列表大小；哈希表放不下的read会进入此列表等待配对，增大可减少临时文件数量
# --io-buffer-size128(MB)读写BAM时两个缓冲区各自的大小，增大可提高IO速度

# 运行完毕后最好再次检查一下mapping率
for i in $tt; do 
  name=$(basename $i) 
  samtools flagstat ${name}.srt_flt.markdup.bam > mapping/${name}.srt_flt.markdup_flagstat
  # 汇总
  mapping=$(cat mapping/${name}.srt_flt.markdup_flagstat | sed -n "5,1p" | cut -d"(" -f 2 | awk '{print $1}')
  prop_mapping=$(cat mapping/${name}.srt_flt.markdup_flagstat | sed -n "9,1p" | awk -F '[(]' '{print $2}' |awk '{print $1}')
  echo -e "$name $prop_mapping/$mapping" >> mapping/total_result_markdup_flagstat.txt
done

# 理论上最终结果的mapping率应该是100%，毕竟去除了unmapped部分和重复的部分
```

## 5. 给参考基因组建立索引，用于snp calling
```bash
# 生成.fai索引，记录每条染色体在 fasta 文件中的精确位置
samtools faidx Malus_domestica.fa
# 生成.dist索引字典，确认基因组有哪些染色体，核对输入文件是否匹配
gatk CreateSequenceDictionary -R Malus_domestica.fa
# 此处的gatk为gatk4,注意检查版本
```

## 6.GATK生成GVCF文件
```bash
# 不分染色体，直接对每个样本生成gVCF。
tt=$(cat wgs_srr.txt)
for i in $tt; do 
  name=$(basename $i) 
  gatk --java-options "-Xmx200g -Djava.io.tmpdir=./tmp" HaplotypeCaller \
    -R ./Malus_domestica.fasta \
    -I ./${name}.srt_flt.markdup.bam \
    -ERC GVCF \
    -O ${name}.g.vcf.gz \
    1>log/log_${name}.txt 2>&1 &
done
# -ERC GVCF：输出gvcf文件，而非一般vcf文件
# 耗时 12h 三个物种，平均5G

# 如果样本数量过多，基因组过大，可以考虑分染色体并行，提高运行效率，但是运行更加复杂=========================
# 根据你的参考基因组创建
cat > chr.list << EOF
chr0
chr1
chr2
chr3
chr4
chr5
chr6
chr7
EOF

cat > sample.list << EOF
sample1
sample2
sample3
EOF

mkdir -p tmp log
# 为每个样本建立目录
cat sample.list | while read sample; do
    mkdir -p ${sample}
done

# HaplotypeCaller 分染色体生成gVCF
cat chr.list | while read chr; do
    cat sample.list | while read sample; do
        gatk --java-options "-Xmx20g -Djava.io.tmpdir=./tmp" HaplotypeCaller \
            -R ./Fragaria_nilgerrensis.fasta \
            -I ./${sample}.srtflt.markdup.bam \
            -L ${chr} \
            -ERC GVCF \
            -O ${sample}/${sample}.${chr}.g.vcf.gz \
            1>log/log_${sample}_${chr}.txt 2>&1
    done
done

```

## 7. CombineGVCFs 合并3个样本的gVCF
```bash
gatk --java-options "-Xmx200g -Djava.io.tmpdir=./tmp" CombineGVCFs \
    -R Fragaria_nilgerrensis.fasta \
    -V SRR14099059.g.vcf.gz \
    -V SRR15691171.g.vcf.gz \
    -V Micromeles_alnifolia.g.vcf.gz \
    -V SRR13526594.g.vcf.gz \
    -O combined_outgroup3.g.vcf.gz \
    1>log_combine.txt 2>&1

# 耗时 ：7min 

# 分染色体的版本 ================================================================================
cat chr.list | while read chr; do
    # 生成每条染色体的gvcf列表
    cat sample.list | while read sample; do
        echo "-V ${sample}/${sample}.${chr}.g.vcf.gz"
    done > gvcf.${chr}.list

    gatk --java-options "-Xmx20g -Djava.io.tmpdir=./tmp" CombineGVCFs \
        -R ./Fragaria_nilgerrensis.fasta \
        $(cat gvcf.${chr}.list) \
        -O ${chr}.combined.g.vcf.gz \
        1>log/log_combine_${chr}.txt 2>&1
done


```

## 8. GenotypeGVCFs群体联合Call SNP
```bash
gatk --java-options "-Xmx200g -Djava.io.tmpdir=./tmp" GenotypeGVCFs \
    -R Fragaria_nilgerrensis.fasta \
    -V combined.g.vcf.gz \
    -O raw.vcf.gz \
    1>log_genotype.txt 2>&1


# 耗时50min

# 分染色体的情况 ================================================================================
cat chr.list | while read chr; do
    gatk --java-options "-Xmx20g -Djava.io.tmpdir=./tmp" GenotypeGVCFs \
        -R ./Fragaria_nilgerrensis.fasta \
        -V ${chr}.combined.g.vcf.gz \
        -O ${chr}.raw.vcf.gz \
        1>log/log_genotype_${chr}.txt 2>&1
done

# MergeVcfs 合并所有染色体VCF
# 生成vcf列表（注意顺序）
cat chr.list | while read chr; do
    echo "-I ${chr}.raw.vcf.gz"
done > raw_vcf.list

gatk --java-options "-Xmx20g -Djava.io.tmpdir=./tmp" MergeVcfs \
    $(cat raw_vcf.list) \
    -O all.merge_raw.vcf \
    1>log/log_merge.txt 2>&1
```

## 9. 提取 SNP
```bash
gatk --java-options "-Xmx20g -Djava.io.tmpdir=./tmp" SelectVariants \
    -R Fragaria_nilgerrensis.fasta \
    -V raw.vcf.gz \
    --select-type SNP \
    -O all.raw.snp.vcf
    
# 耗时 2min
```

# 10. 提取INDEL---视情况可不做
```bash
gatk --java-options "-Xmx20g -Djava.io.tmpdir=./tmp" SelectVariants \
    -R Fragaria_nilgerrensis.fasta \
    -V raw.vcf.gz \
    --select-type INDEL \
    -O all.raw.indel.vcf
    
# 耗时 1.3min
```

## 11. 过滤SNP
```bash
# 统计“硬过滤”相关指标在VCF文件中的分布频率
gatk VariantsToTable \
    -V all.raw.snp.vcf \
    -F CHROM -F POS -F QD -F QUAL -F SOR -F FS -F MQ -F MQRankSum -F ReadPosRankSum \
    -O snp_annotations_gatk.table


gatk --java-options "-Xmx20g -Djava.io.tmpdir=./tmp" VariantFiltration \
    -R Fragaria_nilgerrensis.fasta \
    -V all.raw.snp.vcf \
    -filter "QD < 2.0" --filter-name "QD2" \
    -filter "QUAL < 30.0" --filter-name "QUAL30" \
    -filter "SOR > 3.0" --filter-name "SOR3" \
    -filter "FS > 60.0" --filter-name "FS60" \
    -filter "MQ < 40.0" --filter-name "MQ40" \
    -filter "MQRankSum < -12.5" --filter-name "MQRankSum-12.5" \
    -filter "ReadPosRankSum < -8.0" --filter-name "ReadPosRankSum-8" \
    -O all.filter.snp.vcf
    
# 9min

# 上述参数为gatk推荐默认参数，实际可以根据画出来的分布频率图尝试调整
# QUAL  变异置信度的绝对值。综合多种证据的Phred-scaled质量分，代表该位点存在变异的概率。
# QD (Quality by Depth)  信噪比 (Signal-to-Noise Ratio)。将变异置信度(QUAL)除以该位点非ref样本的深度，抵消深度对QUAL值的夸大作用，避免假高QUAL
# SOR (Strand Odds Ratio)  优化的链偏好性指标。用基于似然比的方法定量链偏好程度，值越大偏好性越强。与FS相比，在高深度或极端链偏时更稳健
# FS (FisherStrand)  链偏好性经典指标。用Fisher精确检验计算正负链支持数差异的p值，以Phred分值表示，值越高链偏好越显著
# MQ (RMSMappingQuality) 位点比对质量的综合评估。计算支持该变异所有reads (包括Ref) 比对质量的“均方根”，反映该区域reads整体的平均比对质量
# MQRankSum  支持ref与alt等位基因reads的比对质量秩和检验z-score。负值表示支持alt等位基因的reads比对质量显著低于ref，可能是比对错误（假阳性）
# ReadPosRankSum  支持ref与alt等位基因reads在read中位置的秩和检验z-score。负值表示支持alt基因的reads更倾向在read的两端，可能是片段化等造成的假阳性



# 计算仅包含通过缺失率过滤的位点的深度并输出：
vcftools \
--gzvcf all.filter.snp.vcf \
--max-missing 0.5 \
--site-mean-depth \
--out all_filter_snp_output_prefix.txt

# vcftools进一步过滤
vcftools \
--vcf all.filter.snp.vcf \
--maf 0.05 \
--max-missing-count 0.5 \
--min-meanDP 3 \
--max-meanDP 114 \
--recode --recode-INFO-all \
--hwe 0.001 \
--out all.filter_vcftools_gatk_missing0.5_maf0.05
# 是针对群体遗传层面的控制，主要是对MAF、缺失率和HWE过滤
# 可参考：https://pixy.readthedocs.io/en/latest/guide/pixy_guide.html#create-a-populations-file
# https://www.programmersought.com/article/62766635261/
# --maf 最小等位基因频率为 0.05，去除稀有等位基因 
# --max-missing-count  最大丢失个体数为零
# --hwe 去除不满足哈温平衡 (p<0.001)的位点


# 去除连锁不平衡位点
# 参考：https://www.cog-genomics.org/plink/1.9/data

plink --vcf all.filter_vcftools_gatk_missing0.5_maf0.05.recode.vcf \
      --indep-pairwise 50 10 0.2 \
      --out all.filter_vcftools_gatk_missing0.5_maf0.05.recode.LD.vcf \
      --allow-extra-chr \
      --double-id
      
plink --vcf all.filter_vcftools_gatk_missing0.5_maf0.05.recode.vcf \
      --extract all.filter_vcftools_gatk_missing0.5_maf0.05.recode.LD.vcf.prune.in \
      --recode vcf-iid \
      --out all.filter_vcftools_gatk_missing0.5_maf0.05.recode.LD.pruned \
      --allow-extra-chr \
      --double-id
```



# 下列步骤可以不进行
## 12. 过滤 INDEL---视情况可不做
```bash
gatk --java-options "-Xmx20g -Djava.io.tmpdir=./tmp" VariantFiltration \
    -R Fragaria_nilgerrensis.fasta \
    -V all.raw.indel.vcf \
    --filter-expression "QD < 2.0 || FS > 200.0 || SOR > 10.0 || MQRankSum < -12.5 || ReadPosRankSum < -8.0" \
    --filter-name "INDEL_filter" \
    -O all.filter.indel.vcf
```

## 13. 提取过滤后的 SNP 和 INDEL
```bash
# 过滤后SNP
gatk SelectVariants \
    -V all.filter.snp.vcf \
    --exclude-filtered \
    -O all.filtered.snp.vcf

# 过滤后INDEL---可不做，如果前面没有过滤
gatk SelectVariants \
    -V all.filter.indel.vcf \
    --exclude-filtered \
    -O all.filtered.indel.vcf
```
