---
title: "Detecting introgression from phylogenetic invariant site patterns using machine learning"
authors: Patrick F. McKenzie, Deren A. R. Eaton
year: 2026
citekey: mckenzieDetectingIntrogressionPhylogenetic2026
tags: [paper, literature]
---

<div style="font-size: 28px; color: #C97C7C; margin-top: 0px;">
  Detecting introgression from phylogenetic invariant site patterns using machine learning
</div>

**Authors:** Patrick F. McKenzie, Deren A. R. Eaton  
**Year:** 2026  
**Journal:** Applications in Plant Sciences, 14:e70061  
**DOI:** [10.1002/aps3.70061](https://doi.org/10.1002/aps3.70061)  
**Zotero:** [Open in Zotero](zotero://select/items/@mckenzieDetectingIntrogressionPhylogenetic2026)

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
  <b>Premise:</b> Detecting historical introgression among populations or species from genomic data is a common goal in evolutionary genetics. Most current methods fall into two major categories: network inference and admixture inference. Network inference (e.g., SNaQ) is computationally challenging and typically requires first reducing large genomic datasets into a less informative collection of inferred gene trees. In contrast, admixture inference (e.g., ABBA–BABA tests) can accommodate enormous single-nucleotide polymorphism (SNP) datasets but is restricted to examining subsets of four to five samples at a time. Here, we demonstrate a new approach to evaluate SNP frequencies among quartet samples under a phylogenetic hypothesis (similar to ABBA–BABA tests), while examining all quartet information simultaneously (similar to the network inference methods).<br><br>
  <b>Methods and Results:</b> To do this, our method <b>simcat</b> trains a neural network machine learning model on coalescent simulations to discriminate between introgression scenarios based on learned SNP frequency patterns. We demonstrate the accuracy of simcat to classify introgression events from simulations, evaluate its sensitivity to variation in species tree parameters, and demonstrate its application to an empirical dataset of oak trees (<i>Quercus</i> ser. <i>Virentes</i>).<br><br>
  <b>Conclusions:</b> Our approach represents a first step towards leveraging machine learning to expand phylogenetic invariants–based methods beyond the scale of quartets to a larger phylogenetic context.
</div>

---

# 核心问题：如何在保持计算效率的同时，充分利用全基因组 SNP 数据中的多位点模式信息来检测渐渗
当前系统发育基因组学中检测历史渐渗事件的方法存在两大瓶颈：
(1) 网络推断方法（如 SNaQ）计算复杂度高，难以处理大规模基因组数据；
(2) 溯祖检验方法（如 ABBA-BABA）虽然能处理大规模 SNP 数据，但仅限于 4-5 个样本的成对比较，无法同时利用所有四分类群信息。

## 1. 文献研究类群与使用的数据集

#### **机器学习训练数据集**：
**模拟数据：** 通过 msprime 工具，基于 coalescent 模拟生成四分类群（quartet）系统发育树，包含渐渗与不完全谱系分选两种场景，涵盖不同的分化时间、有效群体大小、渐渗比例等参数组合。最终输入的数据为 **SNP 位点模式频率（site pattern frequency spectra）**，即 quartet 拓扑下所有可能的核苷酸位点模式（如 ABBA、BABA 等）的计数频率

#### **实测数据集：**
 此前发表 的栎属橡树类群 *Quercus* ser. *Virentes*（7个物种）的 RAD-seq 数据，包含数千个 SNP 位点。

---
## 2. 文献使用的主要方法

#### **核心方法：simcat**
simcat 是一个基于机器学习的渐渗检测工具，其工作流程如下：
![](../imag/@Detecting%20introgression%20from%20phylogenetic%20invariant%20site%20patterns%20using%20machine%20learning_2026/file-20260713115733133.png)
1. **训练数据生成：** 使用 msprime 进行 coalescent 模拟，生成两类数据：
   - 纯 ILS 场景（无渐渗）
   - 渐渗场景（不同方向、不同比例、不同时间的渐渗事件）
   
2. **特征提取：** 从模拟的 SNP 矩阵中提取 **系统发育不变量（phylogenetic invariants）**——即各位点模式（site pattern）的频率。对于四分类群，共有 $4^4 = 256$ 种可能的位点模式，对称性约简后得到多个独立频率。
 ![|700](../imag/@Detecting%20introgression%20from%20phylogenetic%20invariant%20site%20patterns%20using%20machine%20learning_2026/file-20260713112749789.png)
![|700](../imag/@Detecting%20introgression%20from%20phylogenetic%20invariant%20site%20patterns%20using%20machine%20learning_2026/file-20260713112546006.png)

**传统方法可能导致的偏差**
(1)  **单个 ABBA-BABA 检验极易产生假阳性**
  渐渗参与方不在 quartet 中时仍能检出显著信号，或信号偏移到渐渗参与方的姐妹群上
  
(2) **方向性无法从单个检验判断**
  D 值的正负符号不能可靠地指示渐渗的实际方向
  
(3) **两种渐渗方向（3→1 与 1→3）的snp矩阵分布模式截然不同**
   simcat 用神经网络同时分析所有层，可以区分出渐渗的方向
   ![](../imag/@Detecting%20introgression%20from%20phylogenetic%20invariant%20site%20patterns%20using%20machine%20learning_2026/file-20260713120909784.png)
 
3. **神经网络分类器：** 训练一个全连接神经网络，以位点模式频率为输入特征，输出为渐渗/非渐渗的二分类（或多分类）结果
 
**监督学习方面**，以 >99.8% 的准确率分类渐渗情景，且自动识别出的最具判别力特征恰好对应 ABBA 和 BABA 型位点模式，说明其能识别出关键信息

**无监督学习方面**，t-SNE 在完全不知晓渐渗标签的情况下，将四尖端树（12 种情景）和五尖端树（24 种情景）的模拟数据分别嵌入二维空间后，同一种渐渗情景的样本自然聚集成紧密的簇，不同情景的簇彼此清晰分离。
![](../imag/@Detecting%20introgression%20from%20phylogenetic%20invariant%20site%20patterns%20using%20machine%20learning_2026/file-20260713115209115.png)


### 结果准确度展示以及与此前已发表结果的对比
- 渐渗比例越大，模型检测的准确度越高，SNP 数量超过 10,000 后准确率趋于饱和，说明中低覆盖度的基因组数据即可满足推断需求。同时，与渐渗无关的其它系统发育树参数发生变化时，模型的准确率波动不大，仅在训练数据的边界区域（极端 Ne 或极端 ILS）准确率下降。
- 在图C展示的一个8tips的系统发育树的所有渐渗可能中， 约 90% 的渐渗情景达到 100% 准确率。错误集中在约 10% 的困难情景中，且仅表现为两种类型——方向颠倒（识别对了边对但箭头反了）或偏移到临近边（将内部供体边误判为其子边）。
- **实证验证：** 在美国活橡树（_Quercus_ ser. _Virentes_）数据上，与 Eaton et al. (2015) 的已有结论完全吻合，验证了方法在真实数据上的有效性
![](../imag/@Detecting%20introgression%20from%20phylogenetic%20invariant%20site%20patterns%20using%20machine%20learning_2026/file-20260713120413158.png)

---

## 3. 文献的主要内容与理论

**2）主要结论：**
- **simcat 在模拟数据上表现优异：** 能够高准确度地区分渐渗与 ILS 场景。
- **对参数变化具有鲁棒性：** simcat 在广泛的物种树参数范围内（不同分化时间、群体大小、渐渗比例）保持稳定的分类性能。
- **实证应用验证：** 在 *Quercus* ser. *Virentes* 数据中，simcat 成功检测到已知的渐渗信号，结果与之前的 ABBA-BABA 和 HyDe 分析一致，但 simcat 能同时利用所有 quartet 信息。
- **方法优越性：** 相比 ABBA-BABA 等传统方法，simcat 能够同时考虑所有位点模式信息（而非仅 ABBA/BABA 两种模式），更全面地利用 SNP 数据中的系统发育信号。

**3） 理论创新点：**
- **首次将机器学习引入系统发育不变量框架：** 传统的系统发育不变量方法依赖显式的统计检验（如 SVD quartet），simcat 通过神经网络隐式地学习不变量模式，突破了传统方法对显式公式的依赖。
- **从 quartet 到多物种的扩展潜力：** 虽然当前实现仍限于四分类群，但该方法框架理论上可以扩展到更大的分类群规模，只需调整输入特征维度（位点模式数量随分类群数指数增长）。

---

## 4. 讨论


**局限与改进空间：**
1. **当前仅限四分类群：** 扩展到 5 个或更多分类群时，位点模式数呈指数增长（$4^N$），可能面临维度灾难。需要探索降维策略或更高效的网络架构。
2. **模拟-现实的差距：** 训练数据完全基于 coalescent 模拟，模型在真实数据上的泛化能力取决于模拟对真实进化过程的逼近程度。若真实数据存在模拟未涵盖的复杂因素（如选择、群体结构变化、重组率变异等），分类性能可能下降。
3. **缺乏不确定性量化：** 当前方法仅给出分类结果，未提供完整的后验概率分布或置信区间，这在实证研究中可能限制其解释力。