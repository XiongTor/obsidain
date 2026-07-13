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
 此前发表的栎属橡树类群 *Quercus* ser. *Virentes*（7个物种）的 RAD-seq 数据，包含数千个 SNP 位点。

---
## 2. 文献使用的主要方法

#### **核心方法：simcat**
simcat 是一个基于机器学习的渐渗检测工具，其工作流程如下：

1. **训练数据生成：** 使用 msprime 进行 coalescent 模拟，生成两类数据：
   - 纯 ILS 场景（无渐渗）
   - 渐渗场景（不同方向、不同比例、不同时间的渐渗事件）
   
2. **特征提取：** 从模拟的 SNP 矩阵中提取 **系统发育不变量（phylogenetic invariants）**——即各位点模式（site pattern）的频率。对于四分类群，共有 $4^4 = 256$ 种可能的位点模式，对称性约简后得到多个独立频率。

3. **神经网络分类器：** 训练一个全连接神经网络，以位点模式频率为输入特征，输出为渐渗/非渐渗的二分类（或多分类）结果。

4. **显著性检验：** 通过模拟零分布（null distribution），对分类结果进行显著性评估。

#### **对比方法：**
- **ABBA-BABA (D-statistic)：** 经典的渐渗检测方法，基于四分类群 SNP 频率差异
- **D3 统计量：** 扩展的 D-statistic 变体
- **HyDe：** 基于系统发育不变量比率的杂交检测方法
- **SNaQ：** 基于网络推断的方法（属于 network inference 范畴）

#### **评估指标：**
- 分类准确率（accuracy）
- ROC 曲线下面积（AUC）
- 对物种树参数（分化时间、群体大小）的敏感性分析
- 假阳性率（FPR）与真阳性率（TPR）

---

## 3. 文献的主要内容与理论

**2）主要结论：**
- **simcat 在模拟数据上表现优异：** 能够高准确度地区分渐渗与 ILS 场景，AUC 值接近 1.0。
- **对参数变化具有鲁棒性：** simcat 在广泛的物种树参数范围内（不同分化时间、群体大小、渐渗比例）保持稳定的分类性能。
- **实证应用验证：** 在 *Quercus* ser. *Virentes* 数据中，simcat 成功检测到已知的渐渗信号，结果与之前的 ABBA-BABA 和 HyDe 分析一致，但 simcat 能同时利用所有 quartet 信息。
- **方法优越性：** 相比 ABBA-BABA 等传统方法，simcat 能够同时考虑所有位点模式信息（而非仅 ABBA/BABA 两种模式），更全面地利用 SNP 数据中的系统发育信号。

**3） 理论创新点：**
- **首次将机器学习引入系统发育不变量框架：** 传统的系统发育不变量方法依赖显式的统计检验（如 SVD quartet），simcat 通过神经网络隐式地学习不变量模式，突破了传统方法对显式公式的依赖。
- **从 quartet 到多物种的扩展潜力：** 虽然当前实现仍限于四分类群，但该方法框架理论上可以扩展到更大的分类群规模，只需调整输入特征维度（位点模式数量随分类群数指数增长）。
- **模拟-训练-预测范式：** 建立了"coalescent 模拟生成训练数据 → 神经网络学习 → 实证数据分类"的完整工作流，为系统发育推断中 ML 方法的应用提供了范例。

**4） 与既有研究的关系：**
- **继承 ABBA-BABA 的思想：** simcat 与 ABBA-BABA 共享"基于 SNP 频率比较四分类群"的基本逻辑，但 simcat 利用所有位点模式而非仅 ABBA/BABA。
- **超越传统不变量方法：** 相比 SVD quartet 等需要显式推导不变量的方法，ML 方法可以自动发现数据中的判别模式。
- **与 HyDe 的互补：** HyDe 也基于系统发育不变量，但使用显式统计公式；simcat 的 ML 方法可能捕捉到 HyDe 无法显式建模的复杂模式。
- **处于网络推断与溯祖检验之间：** simcat 兼具 ABBA-BABA 的计算效率（可处理大规模 SNP）和网络推断方法的多位点信息利用能力。

---

## 4. 思考

**优势与潜力：**
1. simcat 的模拟-训练-预测范式非常灵活，理论上可以通过调整模拟参数来适配不同的进化场景（如不同突变模型、群体历史等）。
2. ML 方法的一个关键优势是无需显式推导不变量公式——神经网络可以自动发现数据中的高维判别模式，这对于复杂进化场景尤为有价值。
3. 该方法填补了 ABBA-BABA（仅能用 2 种位点模式）与网络推断（计算成本高）之间的空白，对大规模基因组数据的渐渗分析具有实际意义。

**局限与改进空间：**
1. **当前仅限四分类群：** 扩展到 5 个或更多分类群时，位点模式数呈指数增长（$4^N$），可能面临维度灾难。需要探索降维策略或更高效的网络架构。
2. **模拟-现实的差距：** 训练数据完全基于 coalescent 模拟，模型在真实数据上的泛化能力取决于模拟对真实进化过程的逼近程度。若真实数据存在模拟未涵盖的复杂因素（如选择、群体结构变化、重组率变异等），分类性能可能下降。
3. **缺乏不确定性量化：** 当前方法仅给出分类结果，未提供完整的后验概率分布或置信区间，这在实证研究中可能限制其解释力。
4. **与 QuIBL 的比较：** 考虑到本项目（Rosaceae cytonuclear）当前使用 QuIBL 进行渐渗/ILS 判别，simcat 提供了一个有趣的替代方案——QuIBL 基于期望树形分布，而 simcat 基于位点模式频率，两者在理论上可以互补验证。

**对本项目的启示：**
- simcat 的思路可以启发我们对 Rosaceae 数据的分析方法改进：是否可以通过模拟训练一个适用于蔷薇科特定进化场景的分类器？
- simcat 与 QuIBL 的结果可以进行交叉验证，增强渐渗推断的可信度。