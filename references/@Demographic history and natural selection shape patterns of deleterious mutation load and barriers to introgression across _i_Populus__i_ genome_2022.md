---
title: "Demographic history and natural selection shape patterns of deleterious mutation load and barriers to introgression across <i>Populus</i> genome"
authors: Shuyu Liu, Lei Zhang, Yupeng Sang, Qiang Lai, Xinxin Zhang, Changfu Jia, Zhiqin Long, Jiali Wu, Tao Ma, Kangshan Mao, Nathaniel R Street, Pär K Ingvarsson, Jianquan Liu, Jing Wang
year: 2022
citekey: liuDemographicHistoryNatural2022
tags: [paper, literature]
---

<div style="font-size: 28px; color: #C97C7C; margin-top: 0px;">
  Demographic history and natural selection shape patterns of deleterious mutation load and barriers to introgression across <i>Populus</i> genome
</div>

**Authors:** Shuyu Liu, Lei Zhang, Yupeng Sang, Qiang Lai, Xinxin Zhang, Changfu Jia, Zhiqin Long, Jiali Wu, Tao Ma, Kangshan Mao, Nathaniel R Street, Pär K Ingvarsson, Jianquan Liu, Jing Wang  
**Year:** 2022  
**Zotero:** [Open in Zotero](zotero://select/items/@liuDemographicHistoryNatural2022)

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
  Hybridization and resulting introgression are important processes shaping the tree of life and appear to be far more common than previously thought. However, how the genome evolution was shaped by various genetic and evolutionary forces after hybridization remains unresolved. Here we used whole-genome resequencing data of 227 individuals from multiple widespread Populus species to characterize their contemporary patterns of hybridization and to quantify genomic signatures of past introgression. We observe a high frequency of contemporary hybridization and confirm that multiple previously ambiguous species are in fact F1 hybrids. Seven species were identified, which experienced different demographic histories that resulted in strikingly varied efficacy of selection and burdens of deleterious mutations. Frequent past introgression has been found to be a pervasive feature throughout the speciation of these Populus species. The retained introgressed regions, more generally, tend to contain reduced genetic load and to be located in regions of high recombination. We also find that in pairs of species with substantial differences in effective population size, introgressed regions are inferred to have undergone selective sweeps at greater than expected frequencies in the species with lower effective population size, suggesting that introgression likely have higher potential to provide beneficial variation for species with small populations. Our results, therefore, illustrate that demography and recombination have interplayed with both positive and negative selection in determining the genomic evolution after hybridization.
</div>

---

## 1. 文献研究类群与使用的数据集
杨属(*Populus*)
采样227个个体，获得全基因组重测序数据


## 2. 文献使用的主要方法
#### Call SNP
选取*P. tremula*的全基因组数据为参考，其余数据使用32×重测序，进行Call  SNP，获得了8,966,513个SNP
使用NJ法建树



## 3. 文献的主要内容与理论
#### 核心问题：
杂交发生后，进入受体物种的“外来基因片段”面临怎样的命运？是什么进化力量（如遗传漂变、自然选择、重组率）决定了哪些片段被保留，哪些被淘汰？
#### 文章目的：
利用227个个体，12种的杨属植物个体全基因组重测序数据，表征杨属的当代杂交模式，并量化过去渗入的基因组特征。
#### 整体路线
（1）先用全基因组数据理清物种分类和当代杂交状态；
（2）比较不同物种的种群历史和遗传负荷差异；
（3）定位基因组上的历史渗入区域；
（4）最后，将渗入区域与重组率、遗传负荷、选择信号进行关联分析，揭示选择力量的作用机制。

#### （1）先用全基因组数据理清物种分类和当代杂交状态
通过SNP数据进行PCA聚类分析，表明了有7个物种可以明显的被分离开，另外5个物种被认为是这7个物种的杂交后代

通过祖源推断与fastSTRUCTURE，叶绿体，线粒体系统发育树以及杂合性和杂交指数表明：许多过去被认为是一个独立“种”的杨树，实际上是不同物种间自发产生的**杂交种**。
|**杂交种名称 (子代)**|**母本 (Maternal)**|**父本 (Paternal)**|**杂交程度 / 备注**|
|---|---|---|---|
|**毛白杨** (_P. tomentosa_)|响叶杨 (_P. adenopoda_)|银白杨 (_P. alba_)|自发产生的 $F_1$ 代杂交种|
|**西藏杨** (_P. tibetica_)|圆叶杨 (_P. rotundifolia_)|银白杨 (_P. alba_)|$F_1$ 代杂交种|
|**五莲杨** (_P. wulianensis_)|山杨 (_P. davidiana_)|响叶杨 (_P. adenopoda_)|$F_1$ 代杂交种|
|**宁陕杨** (_P. ningshanica_)|山杨 (_P. davidiana_)|响叶杨 (_P. adenopoda_)|$F_1$ 代杂交种（母本源自不同的山杨群体）|
|**银灰杨** (_P. canescens_)|欧洲山杨 (_P. tremula_)|银白杨 (_P. alba_)|包含 $F_1$ 代及与亲本回交的后代|
|**山杨×圆叶杨杂交区**|山杨 (_P. davidiana_)|圆叶杨 (_P. rotundifolia_)|存在大量具有混合基因型的杂交个体|

## 4. 思考





