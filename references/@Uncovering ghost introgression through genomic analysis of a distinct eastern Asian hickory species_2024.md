---
title: "Uncovering ghost introgression through genomic analysis of a distinct eastern Asian hickory species"
authors: Wei-Ping Zhang, Ya-Mei Ding, Yu Cao, Pan Li, Yang Yang, Xiao-Xu Pang, Wei-Ning Bai, Da-Yong Zhang
year: 2024
citekey: zhangUncoveringGhostIntrogression2024
tags: [paper, literature]
---

<div style="font-size: 28px; color: #C97C7C; margin-top: 0px;">
  Uncovering ghost introgression through genomic analysis of a distinct eastern Asian hickory species
</div>

**Authors:** Wei-Ping Zhang, Ya-Mei Ding, Yu Cao, Pan Li, Yang Yang, Xiao-Xu Pang, Wei-Ning Bai, Da-Yong Zhang  
**Year:** 2024  
**Zotero:** [Open in Zotero](zotero://select/items/@zhangUncoveringGhostIntrogression2024)

---

<div style="
  border-radius: 8px;
  background-color: #2f2d2d;
  padding: 15px 20px;
  margin-top: 15px;
  color: #ddd6d6;
  line-height: 1.6;
  font-size: 16px;
">
  <div style="font-size: 22px;font-weight: bold; color: #bbb3b3; margin-bottom: 8px;">
    ❝ Abstract
  </div>
  Ghost introgression, or the transfer of genetic material from extinct or unsampled lineages to sampled species, has attracted much attention. However, conclusive evidence for ghost introgression, especially in plant species, remains scarce. Here, we newly assembled chromosome-level genomes for both Carya sinensis and Carya cathayensis, and additionally re-sequenced the whole genomes of 43 C. sinensis individuals as well as 11 individuals representing 11 diploid hickory species. These genomic datasets were used to investigate the reticulation and bifurcation patterns within the genus Carya (Juglandaceae), with a particular focus on the beaked hickory C. sinensis. By combining the D-statistic and BPP methods, we obtained compelling evidence that supports the occurrence of ghost introgression in C. sinensis from an extinct ancestral hickory lineage. This conclusion was reinforced through the phylogenetic network analysis and a genome scan method VolcanoFinder, the latter of which can detect signatures of adaptive introgression from unknown donors. Our results not only dispel certain misconceptions about the phylogenetic history of C. sinensis but also further refine our understanding of Carya's biogeography via divergence estimates. Moreover, the successful integration of the D-statistic and BPP methods demonstrates their efficacy in facilitating a more precise identification of introgression types.
</div>

---
# 主要科学问题：喙核桃 *C. sinensis* 的系统发育位置仍不确定，山核桃属内存在杂交渐渗以及幽灵渐渗现象
## 1. 文献研究类群与使用的数据集

#### **研究类群：**
胡桃科（Juglandaceae）山核桃属(*Carya*)
外群为 枫杨(*Pterocarya stenoptera*）

#### **数据组成：**
**全基因组数据：**
新组装2个：喙核桃(*C. sinensis*)及其同属物种山核桃(*C. cathayensis*)
公共数据库1个：美国山核桃(*C. illinoinensis*)

**重测序数据：**
43 个 *C. sinensis* 个体
11种二倍体山核桃属物种各1个个体,涵盖了全部6种东亚山核桃属物种以及5种北美山核桃属物种

**SNP数据集:**
12 个二倍体 *Carya* 物种 + 外群 *P. stenoptera*

东亚(EA) 支系包括 *C. sinensis, C. cathayensis, C. dabieshanensis, C. hunanensis, C. kweichowensis, C. poilanei, C. tonkinensis*
北美(NA) 支系包括 *C. aquatica, C. cordiformis, C. illinoinensis, C. laciniosa, C. ovata*。

## 2. 文献使用的主要方法

### 2.1**基因组组装与注释**
通过三套全基因组数据，鉴定了山核桃属中的两次多倍化事件
![](../imag/@Uncovering%20ghost%20introgression%20through%20genomic%20analysis%20of%20a%20distinct%20eastern%20Asian%20hickory%20species_2024/file-20260629110122553.png)
### 2.2 **物种树重建**
**分不同的数据集进行**
####  4 物种（*C. sinensis, C. cathayensis, C. illinoinensis* + 外群）：
- ASTRAL-Pro（DNA 序列）
- whole-genome microsynteny（Zhao et al., 2021）
- local gene content（Pett et al., 2019）
以上三种方法均得到了完全一致的系统发育树结果，喙核桃与东亚支系聚为姐妹
![|500](../imag/@Uncovering%20ghost%20introgression%20through%20genomic%20analysis%20of%20a%20distinct%20eastern%20Asian%20hickory%20species_2024/file-20260629110015466.png)
####  12 物种扩展集：
CVTree v4.0（全蛋白）
ASTRAL v5.7.4（7398 单拷贝基因）
SVDquartets（19,556 SNP）
PhyloNet（网状树）
同样的，得到了一致的系统发育树，喙核桃与东亚支系聚为姐妹
PhyloNet结果鉴定到两次杂交事件,其中喙核桃被鉴定为一个网状节点
![](../imag/@Uncovering%20ghost%20introgression%20through%20genomic%20analysis%20of%20a%20distinct%20eastern%20Asian%20hickory%20species_2024/file-20260629110433026.png)
![|400](../imag/@Uncovering%20ghost%20introgression%20through%20genomic%20analysis%20of%20a%20distinct%20eastern%20Asian%20hickory%20species_2024/file-20260629110621235.png)
### 2.3 **渐渗检测：**

**D-statistic：对 30 个有根四分体 
- P1: *C. sinensis*
- P2: 5 EA
- P3: 6 NA
- O: *P. stenoptera* 
只能判断有无渐渗，**无法区分**非姐妹种间渐渗 vs 幽灵支系渐渗

 **BPP v4.6.2（MSci 模型）**：
 全似然法，直接分析多位点序列（利用基因树拓扑+分支长度），考虑到其计算量，只选取了三个具有代表性的物种三联体进行后续分析
 ((C. sinensis, C. cathayensis), C. illinoinensis)
 ((C. sinensis, C. tonkinensis), C. illinoinensis)
 ((C. sinensis, C. kweichowensis), C. cordiformis)
 model6的对数边际似然值最高，即支持存在祖先的幽灵渐渗事件
 ![](../imag/@Uncovering%20ghost%20introgression%20through%20genomic%20analysis%20of%20a%20distinct%20eastern%20Asian%20hickory%20species_2024/file-20260629111840745.png)

### 2.4 **分化时间估算：**
采用MSci 模型的 BPP 实现分化时间估计,优势是可以考虑到渐渗的影响
幽灵谱系从山核桃属的共同祖先中分化出来,估计分化时间约在 5.42 Ma。这表明该已灭绝祖先在那一时期存在于东亚,为遗传物质在 2.72 Ma 时渗入 C. sinensis 提供了机会
![](../imag/@Uncovering%20ghost%20introgression%20through%20genomic%20analysis%20of%20a%20distinct%20eastern%20Asian%20hickory%20species_2024/file-20260629112217306.png)

### 2.5 **可能的幽灵渐渗基因**
VolcanoFinder利用SNP数据集检测渐渗基因，经过全基因组扫描后，通过复合似然比(CLR) 方法识别出44个不同的20 kb区块,涵盖36个候选基因
对这36个基因的基因本体(GO) 富集分析表明,它们参与了多种生物学过程,如昼夜节律 的正向调控、红光和远红光的光转导、远红光信号通路以 及防御反应
![|650](../imag/@Uncovering%20ghost%20introgression%20through%20genomic%20analysis%20of%20a%20distinct%20eastern%20Asian%20hickory%20species_2024/file-20260629112750317.png)
## 3. 文献的主要内容与理论

### 理论创新点
- **整合 D-statistic + BPP 的策略**：用 D-statistic 快速筛查有无渐渗，再用 BPP 全似然法，**精准鉴别渐渗类型**
- 采用了**对基因流稳健的基因组结构信号（microsynteny / gene content）作为物种树先验来构建系统发育树**
- 提出 VolcanoFinder 检出的 36 基因中 20 个在其他近缘/外群物种无直系同源，可能正是**幽灵供体已灭绝**留下的基因组印记
