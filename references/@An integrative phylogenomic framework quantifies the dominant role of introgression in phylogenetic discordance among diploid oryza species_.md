---
title: "An integrative phylogenomic framework quantifies the dominant role of introgression in phylogenetic discordance among diploid oryza species"
authors: Hui-fang Li, Shuang-feng Dai, Tian-long Fang
year: 
citekey: liIntegrativePhylogenomicFramework
tags: [paper, literature]
---

<div style="font-size: 28px; color: #C97C7C; margin-top: 0px;">
  An integrative phylogenomic framework quantifies the dominant role of introgression in phylogenetic discordance among diploid oryza species
</div>

**Authors:** Hui-fang Li, Shuang-feng Dai, Tian-long Fang  
**Year:**   
**Zotero:** [Open in Zotero](zotero://select/items/@liIntegrativePhylogenomicFramework)

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
  Background: Phylogenomic studies frequently reveal widespread gene tree discordance, primarily arising from incomplete lineage sorting (ILS) and hybridization and/or introgression. Disentangling these processes is especially challenging in rapidly radiating lineages. The genus Oryza, with its rapid diversification and multiple genome types, exemplifies this pervasive phylogenetic incongruence. We integrated multiple genomic datasets including whole-genome resequencing, transcriptomes, and published genomes from diploid Oryza species. Concatenation and multispecies coalescent analyses recovered a robust, congruent species tree, placing the FF and GG genome groups as a monophyletic basal clade, followed by successive divergence of the EE, CC, BB, and AA lineages, a topology differing from some prior hypotheses.
</div>

---

## 1. 文献研究类群与使用的数据集

#### **研究类群：**
稻属
11属27种，包含6个二倍体属（AA, BB, CC, EE, FF, GG）和5个多倍体属（BBCC, CCDD, HHJJ,  HHKK, KKLL）

#### **原始数据集：**
**自测数据**包含**基因组重测序数据**与**转录组数据**
同时抓取了二倍体属的网上公共数据集的**染色体级别基因组数据**。

#### **直系同源基因数据集：**
通过OrthoFinder筛选组装出的包含**3973 single-copy orthologs**

#### **SNP数据获取**


## 2. 文献使用的主要方法
#### 建树方法：
串联法建树：RAxML建树，分别使用SNP和单拷贝直系同源基因建树
溯祖法建树：RAxML建立基因树，ASTRALⅢ建立物种树

同时也建立了质体树：使用RAxML建立

#### 基因树不一致分析方法： 
Phyparts
SplitsTree
PhyloNet
D-statistics (Dfm与Dxy): Dfm滑动窗口确定具体基因位置
QuIBL： BIC显著性判断ILS，IH占比
GO富集
TreeMix（推断历史群体层面基因流动的方向和规模）
r8s （推断分化时间）




## 3. 文献的主要内容与理论
**1） 核心问题**：稻属早期研究难以得到一致的令人信服的系统发育结果。前人有提出稻属存在严重的杂交渐渗现象，但是并没有具体量化。
**2）主要结论：** 
- 稻属内存在强烈的杂交渐渗现象，在杂交渐渗和ILS中，文章的结论支持杂交渐渗占据主导地位，约占总比的74.17%。
- 通过染色体级别基因组数据，鉴定出具体的渐渗基因，通过GO富集发现这类基因主要富集在环境适应（应激反应、次级代谢）相关的功能上，证明了网状进化在水稻适应环境中的关键作用
**3） 理论创新点：** 解决了如何量化杂交渐渗的问题，同时鉴定出了具体的渐渗基因，有助于了解杂交渐渗在物种适应与演化中起到的作用。同时也为后续类似研究提供了范例

## 4. 思考
文章中目前最大的问题是，似乎忽略了**系统误差带来的干扰**，对于IH占比74.17的结论，几乎是只通过QuIBL的显著率来判断，而没有具体分析其ILS的可能情况，可能存在ILS强度低但是显著的情况，而这种低水平的ILS可能就是背景噪音造成的。同理IH的高占比也没有核实并作出相应解释。
