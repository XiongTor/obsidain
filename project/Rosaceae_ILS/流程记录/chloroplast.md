---
title: chloroplast
date: 2025-09-16
authors: Tao Xiong
tags:
  - 叶绿体流程记录
  - cytonuclear
---

### 说明
本流程主要用于记录chloroplast数据集进行核质冲突分析的全流程

### 处理位置：
`/data/xiongtao/project/Rosaceae/chloroplast`

### 数据集合
蔷薇科一共79采样，另外外类群3采样，总数82，采样率73%

# 1. 叶绿体基因筛选
已经完成前置条件，从NCBI中收集了覆盖外类群的36个叶绿体全基因组作为参考序列，利用hybpiper从WGS或RNA-seq数据中提取出了约86个质体基因用于建立系统发育树

# 2. 系统发育树构建
已经完成mafft与trimal，此处从建树开始
```
#建立所需文件夹
mkdir 05-genetrees 06-genetrees_rt 07-sptree

#iqtree建树
for name in ./04-1-trimal_noNeviusia/*.fasta;do
  tt=$(basename $name .fasta|cut -f1 -d'_')
  echo $tt
  iqtree -s $name -m MFP -B 1000 --bnni -T 10 -pre ./05-genetrees/${tt}
done

#基因树置根
for tree in ./05-genetrees/*.treefile; do
    nn=$(nw_labels -I $tree|grep -c -E "Elaeagnus_angustifolia|Zelkova_schneideriana|Morus_indica")
    if [[ $nn -gt 0 ]]; then
        tt=$(nw_labels -I $tree|grep -E "Elaeagnus_angustifolia|Zelkova_schneideriana|Morus_indica"|paste -sd,)
        mm=$(basename $tree .treefile)
        pxrr -t $tree -g $tt >06-genetrees_rt/${mm}.rt.tre
    else
        Rscript /home/xiongtao/data/scripts/mad_reroot.R $tree
    fi
done

#Astral建立物种树
cat ../06-genetrees_rt/*.tre> rosa_chloroplast_genes.tre

# ASTRAL3
java -jar /home/xiongtao/data/software/ASTRAL-master/Astral/astral.5.7.8.jar -i rosa_chloroplast_genes.tre -o rosa_chloroplast_genes_sp.tre

# ASTRAL_pro3
~/data/software/ASTER/bin/astral-pro3 -u 2 -i rosa_chloroplast_genes.tre -o rosa_chloroplast_sp.tre>log_astral-pro3.txt


pxrr -t rosa_chloroplast_sp.tre -g Elaeagnus_angustifolia,Zelkova_schneideriana,Morus_indica>rosa_chloroplast_sp_rt.tre

#增加族信息，检测单系性
pxrlt -t rosa_chloroplast_sp_rt.tre -c ../old_name.txt -n ../new_name.txt >rosa_chloroplast_sp_rt_rn.tre

Rscript ~/data/scripts/mono.R rosa_chloroplast_sp_rt_rn.tre
```

订正，由于叶绿体序列普遍序列较短，因此使用串联法建树更加合理准确，所以计划重新使用串联法IQTREE建树
```
# orgin
pxcat -s ../04-trimal/*.fasta -p partition.txt -o rosa_chloroplast_supermatrix.fasta

iqtree -s rosa_chloroplast_supermatrix.fasta -m MFP -B 1000 -p partition.txt --bnni -T 15 

#final
pxcat -s ./04-trimal-final/*.fasta -p partition.txt -o rosa_chloroplast_supermatrix.fasta

iqtree -s rosa_chloroplast_supermatrix.fasta -m MFP -B 1000 -p partition.txt --bnni -T 15 

#不加分区文件
iqtree -s rosa_chloroplast_supermatrix.fasta -m MFP -B 1000 --bnni -T 30
```