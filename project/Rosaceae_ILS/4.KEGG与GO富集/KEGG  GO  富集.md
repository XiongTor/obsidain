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

# 相关参考教程
[知乎-基因功能注释](https://zhuanlan.zhihu.com/p/1960020487737439105)
[B站-GO富集分析](https://www.bilibili.com/video/BV1fM411v7y3/?spm_id_from=333.788.recommend_more_video.0&trackid=web_related_0.router-related-2589621-zbgqr.1782474842257.823&vd_source=0014b90d2571a54cece0ba578547267d)

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

# 2. orgDb 数据库构建以及GO富集分析
由于大多数时候我们研究的物种并不是模式物种，无法使用现存的数据库进行后续分析
因此我们可以根据注释信息，自己构建orgDb 数据库 

``` R
# !/usr/bin/Rscript

# date: 2026-06-27

# author: xiongtao

# description: This script is used to perform KEGG and GO enrichment analysis on introgressed genes.

# ======================================================================================================

#### 加载包
library(AnnotationForge)
library(clusterProfiler)
library(GO.db)
library(HDO.db)
library(tidyverse)
library(ggplot2)
library(forcats)
library(viridis)
library(patchwork)
library(enrichplot)

#### 读取注释数据（无论是否构建OrgDb都需要）

annot_file <- "all_OGsgenes.emapper.annotations"

orgdb_dir <- "org.DR.eg.db"

# 读取注释文件，跳过以#开头的行
annot_data <- read.delim(annot_file, comment.char = "#", header = FALSE, stringsAsFactors = FALSE)

# 设置列名

colnames(annot_data) <- c(
  "query", "seed_ortholog", "evalue", "score", "eggNOG_OGs",
  "max_annot_lvl", "COG_category", "Description", "Preferred_name",
  "GOs", "EC", "KEGG_ko", "KEGG_Pathway", "KEGG_Module",
  "KEGG_Reaction", "KEGG_rclass", "BRITE", "KEGG_TC", "CAZy",
  "BiGG_Reaction", "PFAMs"
)

  

# 将"-"替换为NA

annot_data <- annot_data %>%

  dplyr::mutate(across(everything(), ~ifelse(. == "-", NA, .)))

emapper <- annot_data %>%

  dplyr::select(GID=query,Gene_Symbol=Preferred_name,

                GO=GOs,KO=KEGG_ko,Pathway =KEGG_Pathway,

                OG=eggNOG_OGs,Gene_Name =seed_ortholog)

#GID=query,这里的query就是csv文件中的表头信息

  

#这里共提取了gene_info,  gene2go,gene2ko,gene2pathway,gene2symbol

#你用哪些信息就可以进行相应的增减。

#如果你只是做go富集分析，其实gene_info和gene2go就足够了

#gene2symbol在这里感觉纯粹是凑数，这是参考引文2弄的。

#gene2ko，gene2pathway是用来做kegg富集分析的。

  

#提取GID与Gene_Name信息，参考1是将X.4作为Gene_Name，应该是自己定义的信息，这里将seed_ortholog作为Gene_Name信息，你可以根据实际再调整。

gene_info <- dplyr::select(emapper,GID,Gene_Name) %>%

  dplyr::filter(!is.na(Gene_Name)) %>%

  dplyr::distinct(GID, .keep_all = TRUE)

  
#提取GID与GO信息，组成goTable，建库时的goTable需要三列（GID, GO和EVIDENCE），少一列，就会出错.

gene2go <- dplyr::select(emapper,GID,GO) %>%

  separate_rows(GO, sep = ',', convert = F) %>%

  dplyr::filter(!is.na(GO)) %>%

  mutate(EVIDENCE = 'A') %>%

  dplyr::distinct(GID, GO, .keep_all = TRUE)

dim(gene2go)    #查看数据维度。
  

#提取GID与KO信息，这里只有2列信息

gene2ko<- dplyr::select(emapper,GID,KO) %>%

 separate_rows(KO, sep = ',', convert = F) %>%

  dplyr::filter(!is.na(KO)) %>%

  dplyr::distinct(GID, KO, .keep_all = TRUE)

dim(gene2ko)


#提取GID与Pathway信息，这里只有2列信息

gene2pathway<- dplyr::select(emapper,GID,Pathway) %>%

separate_rows(Pathway, sep = ',', convert = F) %>%

  dplyr::filter(!is.na(Pathway)) %>%

  dplyr::distinct(GID, Pathway, .keep_all = TRUE)

 dim(gene2pathway)
  

#提取GID与Gene_Symbol信息，Gene_Symbol是Preferred_name信息，这里只有2列信息

gene2symbol<- dplyr::select(emapper,GID,Gene_Symbol) %>%

  dplyr::filter(!is.na(Gene_Symbol)) %>%

  dplyr::distinct(GID, .keep_all = TRUE)

dim(gene2symbol)

if (!dir.exists(orgdb_dir)) {
  message("正在构建OrgDb数据库...")
  # 使用makeOrgPackage构建OrgDb - 需要给参数命名

  AnnotationForge::makeOrgPackage(gene_info=gene_info,
                                  go=gene2go,
                                  ko=gene2ko,
                                  pathway=gene2pathway,
                                  symbol=gene2symbol,
                                  maintainer='XT <xt@example.com>',
                                  author='XT',
                                  version="0.1",
                                  outputDir=".",
                                  tax_id="4097",
                                  genus="D",
                                  species="R",
                                  goTable="go")

  

  # 安装构建好的包

  install.packages(orgdb_dir, repos = NULL, type = "source")

  message("OrgDb数据库构建完成！")

} else {

  message("OrgDb数据库已存在，跳过构建步骤")

}

# =======================================================================================================
# 加载OrgDb数据库

library(org.DR.eg.db)

# GO富集分析

# 选取Dsuite Fdm值前5%的基因作为候选基因集

gene <- read.csv("SRR10377315_SRR15691171_SRR24154117/SNP_SRR10377315_SRR15691171_SRR24154117_allgene_Fdm.csv", header = T,stringsAsFactors = FALSE, fileEncoding = "GBK")

threshold <- quantile(gene$f_dM_abs,0.9,na.rm = TRUE)

top0.05_genes <- gene[gene$f_dM_abs >= threshold, ]
top0.05_genes_expanded <- top0.05_genes %>%
  separate_rows(gene_id, gene_location, sep = ",")

gene_id <- unique(top0.05_genes_expanded$gene_id) %>%
    gsub("_1", "", .) %>%
    unique()

IH_gene <- paste0("Dichotomanthes_tristaniicarpa-", gene_id)

# 去重，确保每个基因只出现一次
IH_gene <- unique(IH_gene)

# IH_gene <- read.csv("SRR10377315_SRR15691171_SRR24154117/IH_genelist.csv",header = F)
# IH_gene <- IH_gene$V1
# 确认这些基因在 OrgDb 里存在

all_gids <- keys(org.DR.eg.db, keytype = "GID")

gene_diff <- IH_gene[IH_gene %in% all_gids]

# 看看有多少基因匹配上了

cat("输入基因数：", length(IH_gene), "\n")

cat("匹配到OrgDb的基因数：", length(gene_diff), "\n")

# GO 富集分析

ego <- enrichGO(

  gene         = gene_diff,

  universe     = all_gids,          # 背景基因 = OrgDb 里所有基因

  OrgDb        = org.DR.eg.db,

  keyType      = "GID",

  qvalueCutoff = 0.2,

  pvalueCutoff = 0.2,

  ont          = "ALL",

  minGSSize    = 1         # 加上这行，避免基因数少的 term 被过滤

)

head(as.data.frame(ego))

# 分面的点图
p <- dotplot(ego, split = "ONTOLOGY",showCategory=20) +  

  facet_grid(ONTOLOGY~., scale="free")+    ##### 分面展示

  # scale_y_discrete(labels=function(x) str_wrap(x, width = 100))+

  theme(

    axis.text = element_text(size = 1, angle = 0, hjust = 1, vjust = 0.5),

  )

p

ggsave("gomf_0.05.pdf", p, width = 10, height = 12)

```