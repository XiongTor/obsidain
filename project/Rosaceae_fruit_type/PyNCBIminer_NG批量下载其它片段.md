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

# 可选的marker类型，来自于官方GitHub

# Ribosomal: 18S, 28S, ITS
# Chloroplast: rbcL, matK, ndhF, ndhD, ndhI, ndhJ-ndhK-ndhC, psbA-trnH, trnL-trnF, rpoB, rpoC1, atpB


M=ITS
R=./

# (1) 切批次 + 建目录 + 写参数(每批一次 Entrez count)
python -u ../PyNCBIminer_NG/src/DNAseqs/run_fragments.py init \
    -m  $M \
    -s ./test.txt \
    -r $R -e 1760755795@qq.com \
    --batch-size 200
# -m marker的名字
# -r 输出目录
# --batch-size 每批物种数

# (2) 跑 BLAST 迭代(可并行)
python -u ../PyNCBIminer_NG/src/DNAseqs/run_fragments.py mine -m $M -r $R --batches all --jobs 1

# (3) 合并各批次
python ../PyNCBIminer_NG/src/DNAseqs/run_fragments.py merge -m $M -r $R

# (4) 全局控制延伸 + 物种级选择 + 汇总
python ../PyNCBIminer_NG/src/DNAseqs/run_fragments.py filter -m $M -r $R
```