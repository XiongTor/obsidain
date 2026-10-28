---
title: "PyNCBIminer批量抓取下载叶绿体数据"
date: "2026-10-27"
authors: Tao Xiong
tags:
---
### Github
[PyNCBIminer_NG](https://github.com/wyx619/PyNCBIminer_NG) 最新版本，以下流程也是基于这个版本进行
[PyNCBIminer](https://github.com/Xiaoting-Xu/PyNCBIminer)
### 官方作者的论文中的相关流程图
下图为官方作者的一次交流中提供的参考流程图，最终结果并不一定以此为准，但以此为参考，制作了本次流程

![|325](../../imag/PyNCBIminer_NG批量抓取下载叶绿体数据/ref.png)

# 一 . 直接使用图形化界面操作
```bash
uv run --frozen pythonw src\Main_Fluent.py
```

# 二. 使用源码操作
本流程主要使用源码，主要是下载的数据量可能较大，不在windows的本地电脑上运行了
```bash

# | marker | 类型 | Rosales种子 | 蔷薇科记录数 | 是否与质体CDS重复 |

#  ITS — nrDNA — 有 — 17012 — 不重复（核）

#  18S — nrDNA — 无 — 6804 — 不重复

#  28S — nrDNA — 无 — 3861 — 不重复

#  rbcL — cp CDS — 有 — 13571 — 重复

#  matK — cp CDS — 有 — 12112 — 重复

#  ndhF — cp CDS — 有 — 5751 — 重复

#  atpB — cp CDS — 无 — 5531 — 重复

#  ndhD — cp CDS — 无 — 4723 — 重复

#  ndhI — cp CDS — 无 — 4721 — 重复

#  ndhJ-ndhK-ndhC — cp CDS — 无 — 6135 — 重复

#  rpoB — cp CDS — 有 — 5253 — 重复

#  rpoC1exon1/2 — cp CDS — 无 — 5233/5104 — 重复（rpoC1）

#  trnL-trnF — cp 非编码 — 有 — 10428 — 不重复（trnL intron+trnL-F IGS）

#  trnLintron-trnF — cp 非编码 — 无(Actually trnLintron-trnF had no Rosales seed) — 12093 — 不重复

#  psbA-trnH — cp 非编码 — 有(Potentilla chinensis!) — 9121 — 不重复

M=ITS
R=./

# (1) 切批次 + 建目录 + 写参数(每批一次 Entrez count)
python ../PyNCBIminer_NG/src/DNAseqs/run_fragments.py init \
    -m  $M \
    -s ./test.txt \
    -r $R -e 1760755795@qq.com \
    --batch-size 200 \
    --date-from 1990/01/01 --date-to 2026/12/31
# -m marker的名字
# -r 输出目录
# --batch-size 每批物种数

# (2) 跑 BLAST 迭代(可并行)
python ../PyNCBIminer_NG/src/DNAseqs/run_fragments.py mine -m $M -r $R --batches all --jobs 3

# (3) 合并各批次
python ../PyNCBIminer_NG/src/DNAseqs/run_fragments.py merge -m $M -r $R

# (4) 全局控制延伸 + 物种级选择 + 汇总
python ../PyNCBIminer_NG/src/DNAseqs/run_fragments.py filter -m $M -r $R
```