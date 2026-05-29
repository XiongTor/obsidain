---
title: "Phylogenetic resolution and conflict in the species-rich flowering plant family leguminosae"
authors: Rong Zhang, Gregory W Stull, Jian-Jun Jin, Yin-Huan Wang, Ying Guo, Zhi-Yun Yang, Hong-Tao Li, Kai-Lun An, Joseph L M Charboneau, Ryan A Folk, Domingos Cardoso, Luciano Paganucci de Queiroz, Anne Bruneau, Pamela S Soltis, Douglas E Soltis, Stephen A Smith, De-Zhu Li, Ting-Shuang Yi
year: 2025
citekey: zhangPhylogeneticResolutionConflict2025
tags: [paper, literature]
---

<div style="font-size: 28px; color: #C97C7C; margin-top: 0px;">
  Phylogenetic resolution and conflict in the species-rich flowering plant family leguminosae
</div>

**Authors:** Rong Zhang, Gregory W Stull, Jian-Jun Jin, Yin-Huan Wang, Ying Guo, Zhi-Yun Yang, Hong-Tao Li, Kai-Lun An, Joseph L M Charboneau, Ryan A Folk, Domingos Cardoso, Luciano Paganucci de Queiroz, Anne Bruneau, Pamela S Soltis, Douglas E Soltis, Stephen A Smith, De-Zhu Li, Ting-Shuang Yi  
**Year:** 2025  
**Zotero:** [Open in Zotero](zotero://select/items/@zhangPhylogeneticResolutionConflict2025)

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
  Abstract The tree of life is central to evolutionary biology, yet resolving deep, recalcitrant phylogenetic relationships remains challenging due to complex processes such as incomplete lineage sorting (ILS), hybridization, and polyploidization. Although previous phylogenetic studies have advanced our understanding of Leguminosae (Fabaceae), a species-rich and ecologically diverse family, many deep relationships at the tribal and higher levels remain unresolved. Incorporating newly generated genome skimming data for 231 species with previously issued plastid genomic, mitochondrial genomic, and transcriptomic data, we reconstructed a phylogeny of the family using whole plastomes, 39 mitochondrial genes, and 1559 low-copy nuclear genes, achieving dense taxonomic sampling across almost all recognized tribes and major unplaced lineages. Our results supported the monophyly of the six subfamilies and 49 recognized tribes, identified 10 clades worthy of recognition as new tribes in subfamily Papilionoideae, and clarified many contentious relationships. However, nuclear–nuclear and cytonuclear conflicts persist at multiple nodes among trees inferred from different data sets and analytical methods. We proposed the most probable resolution for 22 contentious nodes by applying nuclear gene tree quartet analysis with corroboration from support of nuclear maximum likelihood and ASTRAL trees. Our results indicate that ILS significantly contributes to observed phylogenetic conflicts, whereas gene flow represents an additional and previously underappreciated factor that mainly contributes to cytonuclear conflicts, particularly along the branches of the Angylocalyceae + Dipterygeae + Amburaneae (ADA) clade and Wisterieae. These processes likely underlie recalcitrant phylogenetic relationships, such as those within the 50-kb inversion clade of Papilionoideae. Our study uses multiple data partitions and analytical methods to resolve contentious phylogenetic relationships in Leguminosae, resulting in a robust phylogenomic framework to guide further investigations in this economically important and exceptionally diverse family.
</div>

---

## 1. 文献研究类群与使用的数据集
**研究类群：** 豆科（Leguminosae / Fabaceae）
**数据组成**：
叶绿体数据，收获了覆盖433属，677种的696份质体基因组，其中235个为新测序数据，进行的浅层测序，仅用于组装质体基因组。244个来源于前期研究，187个来自于NCBI
线粒体数据，收获了380属，432种，459份样本的序列，其中211个为新测数据，219个来源于前期研究，29个来自于NCBI
核基因数据，全部来自于前期研究，涵盖333属，463种，收获了1559个低拷贝核基因
同时，线粒体和叶绿体数据集又再次进行了划分，按照包含全部基因，包含编码区与包含非编码区划分为三组，同时又按照是否进行trimal，再次产生了三套数据集，具体分别为：

叶绿体：PCN203、PC81 和 PN122，PCN203‐trimAL、PC81‐trimAL 和 PN122‐trimAL
线粒体：MG39、MC39 和 MI9，MG39‐trimAL、MC39‐trimAL 和 MI9‐trimAL
核基因：Nucl1559

所有数据集均用于系统发育分析，但仅下列三套数据用于了后续分析

|数据集|内容|样本规模|
|---|---|---|
|PCN203|叶绿体全基因组（203个编码+非编码区，510,234 bp）|696个质体组，433属|
|MG39|线粒体39个编码基因（123,624 bp）|459个样本，380属|
|Nucl1559|1559个低拷贝核基因（1,552,253 bp）|463种，333属|

值得一提的是，线粒体数据由于系统发育树的支持率低以及不稳定的问题，没有在后续分析中探讨

## 2. 文献使用的主要方法

**系统发育重建：**
- 最大似然法（ML）：RAxML v8，GTRGAMMA模型，1000次快速自举；PartitionFinder2确定最优分区方案
- 溯祖物种树推断：ASTRAL-III，基于多物种溯祖模型（MSC），局部后验概率（LPP）评估支持度
- 共产生13棵ML树和4棵ASTRAL树

**冲突量化与解析：**

- 基因一致性因子（gCF）：衡量基因树与物种树的不一致程度
- 多态性检验（ASTRAL）：通过四分体拓扑频率（q1/q2/q3）检验节点是否可拒绝多叉假设
- 溯祖模拟：在仅ILS条件下生成1000棵模拟质体基因树，检验质核冲突是否能仅由ILS解释

**ILS、基因流与基因树估计误差（GTEE）的解耦：**
- ILS：用θ值（变异/溯祖单位分支长度之比）衡量
- 基因流：用Reticulation Index（RI）衡量，基于三分体频率偏离MSC零分布来识别
- GTEE：通过模拟序列、重建基因树、统计误差节点比例来量化
- 统计检验：t检验比较冲突节点与一致节点间三者差异；Pearson相关+LOESS回归分析三者与基因树变异的关系
- **值得一提的是，没有说明GTEE,IH,ILS**三者的贡献占比，根据其回归分析的结果，推断应该是GTEE占据了过高的贡献占比而没有展示在主图中

## 3. 文献的主要内容与理论
### 1） 核心问题：
豆科部分类群的系统发育关系长期难以解析，存在强烈的核质冲突和系统发育冲突
### 2）主要结论：
**在系统发育关系梳理上：**
- 强烈支持全部6个亚科和**49个传统识别族**的单系性
- 在蝶形花亚科中新发现**10个值得正式命名为族的支系**（文中标注*，如Vataireoid clade、Dermatophyllum clade、Haplormosia clade等）
- 确定了多个此前位置不明属的系统位置（如Haplormosia、Cabari、Dermatophyllum）
- 但是对于**50-kb倒位支系内部**以及其它部分类群的系统发育位置仍难以确认

**在系统发育冲突层面上：**
- **ILS是主要驱动力**，在深层节点尤为突出；71%的冲突分支受ILS显著影响
- **基因流是次要但不可忽视的因素**，主要贡献于核质冲突，集中于ADA支系和Wisterieae分支；溯祖模拟表明部分质核冲突不能仅由ILS解释
- GTEE在短分支处加剧不确定性，与ILS共同解释了基因树变异的大部分
- 三者在50-kb倒位支系中叠加作用，是该区域持续难以解析的根本原因

## 4. 思考学习
了解到的一些要点：
### （1）如何确定一个系统发育节点是否解析良好，并能够以此判断说明存在的系统发育问题已被解析
1) ML树，即串联法的自展支持率≥90%
2) ASTRAL 后验概率≥ 0.9
3) 核基因树四分体频率高(≥38%)，即一致性高于38%
 不满足这些标准的节点 被归类为不确定节点
 
### (2)针对GTEE,IH,ILS**三者的贡献占比分析，可以加上yi
![](../imag/@Phylogenetic%20resolution%20and%20conflict%20in%20the%20species-rich%20flowering%20plant%20family%20leguminosae_2025/file-20260529171637913.png)





