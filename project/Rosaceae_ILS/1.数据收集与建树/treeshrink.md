---
title: treeshrink
date: 2025-09-14
authors: Tao Xiong
tags:
  - cytonuclear
  - 长枝
---
### 参考文献
[TreeShrink: fast and accurate ](https://bmcgenomics.biomedcentral.com/articles/10.1186/s12864-018-4620-2)
### Github
[treeshrink](https://github.com/uym2/TreeShrink)
### 其它教程
暂无
# treeshrink
treeshink主要用于去掉系统发育树中的长枝，避免**长枝吸引**干扰系统发育的结果。
对于溯祖法，主要作用于基因树
对于串联法，主要作用于物种树

### 1. input

主要输入文件为**基因树**和对应的**序列**（trimal后），需要根据不同的基因创建不同的文件夹，每个文件夹下至少需要一个树文件和一个序列文件，且所有的序列和树文件需要统一名称，例如都叫`input.tre`和`input.fasta`。
值得注意的是，大部分外类群都会被鉴定为长枝，但大部分情况下，外类群是需要保留的。
因此此处建议可以在跑treeshrink之前，去掉基因树中的外类群，序列保持不变，这样就能保留下所有的外类群。

### 2. 运行
```
#将每个基因的外类群序列单独提取出来

#先将每个物种的序列换行符去掉
for name in ./seq/*.fasta;do
  tt=$(basename $name|cut -f1 -d'_')
  seqkit seq $name -w 0 > ./seq_without_outg/${tt}_oneline.fasta
done


for name in ./00-HRS/*.fasta;do
  tt=$(basename $name|cut -f1 -d'.')
  grep -E -A 1 --no-group-separator "Elaeagnus_angustifolia|Zelkova_schneideriana|Morus_indica" $name> ./00-1-HRS_only_outg/${tt}_outgroup.fasta
done


#去除所有基因树中的外类群，并将对应的序列放到对应文件夹中，序列更名为input.fasta，基因树更名为input.tre
for tree in ../02-more100_data/02-genetree_reroot/*.tre;do
  tt=$(basename $tree|cut -f1 -d'.')
  mkdir $tt
  pxrmt -t $tree -n Elaeagnus_angustifolia,Zelkova_schneideriana,Morus_indica>./$tt/input.tre
  pxrms -s ../02-more100_data/00-HRS/${tt}.trimmed.aln.fasta -n Elaeagnus_angustifolia,Zelkova_schneideriana,Morus_indica>./$tt/input.fasta
done

#运行treeshrink,注意路径
nohup run_treeshrink.py -i ./ -t input.tre -a input.fasta -b 20 -q 0.2 > ./input.tree.treeshrinklog.txt &

#只是检测不同梯度是否有变化
run_treeshrink.py  -t ../10-sptree/MI/MI_orthofinder_genetrees.tre -q 0.01 -b 1 -o ./MI_treeshrink_multi -O shrunk_0.01

run_treeshrink.py  -t ../02-more100_data/03-sptree_more100/rosa_ags353_genes.tre -q 0.2 -b 20 -o mm10_treeshrink_multi_1 -O shrunk

run_treeshrink.py  -t ../02-more100_data/03-sptree_more100/rosa_ags353_genes.tre -q 0.01 -b 1 -o mm10_treeshrink_multi_2 -O shrunk

run_treeshrink.py  -t ../02-more100_data/03-sptree_more100/rosa_ags353_genes.tre -q 0.5 -b 50 -o mm10_treeshrink_multi_3 -O shrunk


#更改输出文件名称,增加外类群序列
for name in `ls -d */`;do
  tt=$(basename $name /)
  mv $tt/output.tre $tt/${tt}_trimmed_rt_noout_output.tre
  cat ../02-more100_data/00-1-HRS_only_outg/${tt}_outgroup.fasta>>$tt/output.fasta
  mv $tt/output.fasta $tt/${tt}_trimmed_haveoutg_output.fasta
done


#建树
ls -d */|sed 's/\///g'>namelist.txt

#服务器2
while read -r line;do
  cp ${line}/*output.fasta seq_data 
done<namelist.txt

### 值得注意的是，treeshrink似乎会在去除序列后自动做序列修剪，导致按照此流程，插入的原序列的外类群会和treeshrink后的序列无法对齐，从而导致建树错误，因此建议在此时重新比对修剪


for name in `ls seq_data`;do
  echo $(basename $name .fasta).mafft
  mafft --auto seq_data/$name > mafft/$(basename $name .fasta).mafft
done

for name in ls mafft/*.mafft; do
  echo ${name}.tri.fasta
  output_file="trimal/$(basename "$name" .mafft).tri.fasta"
  trimal -in "$name" -out "$output_file" -automated1
done

for name in trimal/*.fasta;do
  tt=$(basename $name .fasta)
  iqtree -s $name -m MFP -B 1000 --bnni -T 15 -pre ./genetrees/${tt}
done

-----------------
while read -r line;do
  iqtree -s $line/${line}_trimmer_haveout_output.fasta -m MFP -B 1000 --bnni -T 10 -pre ./genetree_b20/${line}
done<namelist.txt

```

另外值得注意的是，在如下代码中
```
nohup run_treeshrink.py -i ./ -t input.tre -a input.fasta -b 20 -q 0.2 > ./input.tree.treeshrinklog.txt &
```
其中-b和-q是两个不同的限制条件，
-b即，如果去掉该序列后，能使得整个树的枝长直径减少20%，则去除
-q在treeshrink中和-α起相同的作用，指的是假阳性率，即只允许有20%的估计误差，相比默认的5%要宽松很多。
此外最终结果，按照目前来看，是取上述两个条件的并集。