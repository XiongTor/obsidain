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

# 数据准备
**参考**：最好是染色体级别全基因组序列，蔷薇科GDR网站中的数据示例，[Argentina anserina](https://www.rosaceae.org/Analysis/24757635)
**准备call snp的物种**：WGS重测序数据即可，尽量选取数据质量较优的（.fq结尾，需要进行质量控制和清洗）

# 软件安装
```bash
# 创建新环境
conda create -n snp

# 安装相关包，已有可以不用安装
conda install bioconda::bwa



```

# 开始分析

## BWA建立基因组index
```bash
bwa index Argentina_anserina_GDR.fasta -p Argentina_anserina
# BWA是将测序数据比对到参考基因组的工具，包含BWA-backtrack, BWA-SW和**BWA-MEM**后者最新，适合70-1Mbp的长序列，最快最准
# 参数-p str 输出前缀（-a str BWT索引构建算法 （is和bwtsw））
# 耗时：基因组大小：400M 16：47 -- 
```

