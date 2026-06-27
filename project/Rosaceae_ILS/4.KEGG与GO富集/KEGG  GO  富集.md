---
title: "KEGG  GO  富集"
date: "2026-06-27"
authors: Tao Xiong
tags:
---
# 名词解释
KEGG与GO本质上都是数据库
**GO数据库**，全称是Gene Ontology(基因本体)，他们把基因的功能分成了三个部分分别是：**细胞组分（cellular component, CC）、分子功能（molecular function, MF）**、**生物过程（biological process, BP）**。利用GO数据库，我们就可以得到我们的目标基因在CC, MF和BP三个层面上，主要和什么有关。

**KEGG数据库**：全程是Kyoto Encyclopedia of Genes and Genomes（京都基因与基因组百科全书）除了对基因本身功能的注释，基因会参与生物体的各个通路，基于通路而形成的数据库就是通路相关的数据库。而KEGG就是通路相关的数据库的一种。

# 正式分析
## 1. 基因功能注释
基于EggNOG-mapper 进行蛋白功能注释，如果已有注释信息可以跳过
注意，如果后续要做GO富集等分析，这里应该注释所有的基因。例如，总计有1000个直系同源基因，其中50个为鉴定出来的渐渗基因，那么这里应该直接注释所有的1000个直系同源基因，方便后续建立自定义的orgDb 数据库。
如果只是想要知道基因功能而不做富集分析，可以不用全部的基因
``` bash
# 安装相关软件包
mamba install -c bioconda -c conda-forge eggnog-mapper

# 下载数据库
# 可以从https://link.zhihu.com/?target=http%3A//eggnog5.embl.de/download/emapperdb-5.0.2/
# 下载后的数据解压后放在同一文件夹中，例如放在eggnog-mapper-database
# 开始注释,可以将所有基因合并到一个文件中一起跑，注意序列的标题行的基因名称需要有辨识性
emapper.py \
  -i all_OGsgenes.fasta \
  -o all_OGsgenes \
  --output_dir ./ \
  --data_dir ../../eggnog-mapper-database \
  --cpu 50 \
  --override \
  -m diamond \
  -d euk \
  --itype CDS



# -d euk 指定使用真核生物数据库

#生成的结果解释：
`.emapper.annotations`：主要的注释结果文件
`.emapper.seed_orthologs`：最佳匹配正交基因信息
`.emapper.hits`：详细比对结果
```

# 2. orgDb 数据库构建
由于大多数时候我们研究的物种并不是模式物种，无法使用现存的数据库进行后续分析
因此我们可以根据注释信息，自己构建orgDb 数据库
```

```