---
title: "The meaning and measure of concordance factors in phylogenomics"
authors: Robert Lanfear, Matthew W. Hahn
year: 
citekey: lanfearMeaningMeasureConcordance
tags: [paper, literature]
---

<div style="font-size: 28px; color: #C97C7C; margin-top: 0px;">
  The meaning and measure of concordance factors in phylogenomics
</div>

**Authors:** Robert Lanfear, Matthew W. Hahn  
**Year:**   
**Zotero:** [Open in Zotero](zotero://select/items/@lanfearMeaningMeasureConcordance)

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
  As phylogenomic datasets have grown in size, researchers have developed new ways to measure biological variation and to assess statistical support for specific branches. Larger datasets have more sites and loci and therefore less sampling variance. While we can more accurately measure the mean signal in these datasets, lower sampling variance is often reflected in uniformly high measures of branch support—such as the bootstrap and posterior probabilitylimiting their utility. Larger datasets have also revealed substantial biological variation in the topologies found across individual loci, such that the single species tree inferred by most phylogenetic methods represents a limited summary of the data for many purposes. In contrast to measures of statistical support, the degree of underlying topological variation among loci should be approximately constant regardless of the size of the dataset. “Concordance factors” (CFs) and similar statistics have therefore become increasingly important tools in phylogenetics. In this review, we explain why CFs should be thought of as descriptors of topological variation rather than as measures of statistical support, and argue that they provide important information about the predictive power of the species tree not contained in measures of support. We review a growing suite of statistics for measuring concordance, compare them in a common framework that reveals their interrelationships, and demonstrate how to calculate them using an example from birds. We also discuss how measures of topological variation might change in the future as we move beyond estimating a single “tree of life” toward estimating the myriad evolutionary histories underlying genomic variation.
</div>

---

# 1. 文献研究重点
随着系统发育基因组学数据集的不断扩大，以及bootstrap运算过程中抽样比例的提升，导致传统的**支持率**不足以发挥其原有的作用。
因此，**一致性因子 (Concordance Factors，CFs）** 逐渐成为衡量拓扑变异度的重要工具。
#### 支持率丧失作用的表现为：
**数据越大，支持率越高**。
支持率这一指标在早期数据集规模很小时，是评估推断结果可靠性的关键工具
![](../imag/@The%20meaning%20and%20measure%20of%20concordance%20factors%20in%20phylogenomics_/ChatGPT%20Image%202026年5月14日%2015_45_22.png)
#### 因此本文的主要内容即：
**如何正确理解和使用"一致性因子"（Concordance Factors, CFs），以及如何区分它们与传统支持度指标的本质差异。**


# 2.目前存在的问题
- **概念混淆问题**：研究者常将CFs**误解为统计支持度**的替代指标，而实际上CFs是描述生物学参数的统计量，与支持度在本质上不同
- **方法多样性问题**：现有多种估计CFs的方法（gCF、qCF、sCF）各有优缺点，缺乏统一的比较框架
- **基因树估计误差**：单个基因座的对齐序列较短，基因树推断本身存在误差，会系统性地扭曲CFs的估计值
- **可视化挑战**：如何直观呈现拓扑异质性，至今没有理想方案

# 3. 文献主要内容与理论

#### 3.1 概念澄清与重新定位
- **支持度（如bootstrap）**：随数据量增加趋向最大值，度量我们对分支存在的统计置信度
- **CFs**：随数据量增加趋于稳定（真实生物学参数的估计趋于精确），度量实际生物学变异的程度
#### 3.2 提出"一致性向量"（Concordance Vector）框架
正式提出并定义了"一致性向量"（concordance vector，用符号ψ表示），将每个内部分支的拓扑变异信息压缩为四个分量：
- **ψ₁**：与物种树一致的基因树比例（即经典的CF/一致性因子）
- **ψ₂**：与第一类不一致拓扑匹配的比例（不一致性因子1）
- **ψ₃**：与第二类不一致拓扑匹配的比例（不一致性因子2）
- **ψ₄**：与物种树不一致且不属于ψ₂或ψ₃类型的所有其他拓扑的比例
这个四元向量满足 ψ₁ + ψ₂ + ψ₃ + ψ₄ = 1，为不同方法的比较提供了一个通用框架。


## 4. 思考





