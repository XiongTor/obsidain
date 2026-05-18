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
- **支持度（如bootstrap）**：随数据量增加趋向最大值，度量我们对分支存在的统计置信度，即我们对这个分支在统计学上的正确性有多大信心。或者说对于数据中噪音的鲁棒性？
- **CFs**：随数据量增加趋于稳定（真实生物学参数的估计趋于精确），度量实际生物学变异的程度，数值大小与好坏无关，只反映数据本身的属性。**其无法提供一个分支是否为真实分支的概率信息,因为物种树中的真实分支几乎可以具有任何 CF 值**
- 集合支持率与CFs来看，高支持率意味着当前的数据集有多确定当前分支的出现频率高于任何冲突分支的冲突频率。
![](../imag/@The%20meaning%20and%20measure%20of%20concordance%20factors%20in%20phylogenomics_/file-20260515164725009.png)
#### 3.2 提出"一致性向量"（Concordance Vector）框架
正式提出并定义了"一致性向量"（concordance vector，用符号ψ表示），将每个内部分支的拓扑变异信息压缩为四个分量：
- **ψ₁**：与物种树一致的基因树比例（即经典的CF/一致性因子）
- **ψ₂**：与第一类不一致拓扑匹配的比例（不一致性因子1）
- **ψ₃**：与第二类不一致拓扑匹配的比例（不一致性因子2）
- **ψ₄**：与物种树不一致且不属于ψ₂或ψ₃类型的所有其他拓扑的比例
这个四元向量满足 ψ₁ + ψ₂ + ψ₃ + ψ₄ = 1，为不同方法的比较提供了一个通用框架。
**在评估过程中，只考虑拓扑关系，不考虑枝长，尽管枝长本身也是一种变异来源**
一致性向量试图应对的挑战是:**为单个分支相关的一致性因子(CF)和不一致性因子(DF)提供一种紧凑且有意义的总结**

![](../imag/@The%20meaning%20and%20measure%20of%20concordance%20factors%20in%20phylogenomics_/file-20260514162735207.png)

#### 3.3 CFs的作用
1) **帮助我们判断，物种树的哪些分支对于预测"任意一个基因座的历史"是有用的，哪些是没用的**

| 分支                   | CF   | 预测能力 | 实际意义             |
| -------------------- | ---- | ---- | ---------------- |
| Palaeognathae 古颌鸟类   | 100% | 极强   | 研究任何基因都可以信任物种树   |
| Elementaves **元素鸟类** | 0.1% | 几乎没有 | 物种树对这里的基因历史毫无预测力 |

2) **帮助选择有效的外类群：**
- **一个有效的外类群应该是在每一个基因树上都是外类群,即外类群的CF应为100%。选择不具备这一特性的外类群可能会误导系统发育推断**

## 3.4 具体如何计算
从三个主要方法来探讨：
- gCF
- qCF
- sCF
##### 方法一：基因一致性因子（gCF）
**输入：** 每个基因座的完整基因树拓扑  
**计算原理：**
1. 对每个基因座，使用最大似然等方法推断一棵完整的基因树
2. 对物种树的每个内部分支，判断每棵基因树是否属于ψ₁、ψ₂、ψ₃或ψ₄
3. **对于一个分支，只有当该分支两侧的四个类群（A、B、C、D）在基因树中都是单系的时，该基因树才是"决定性的"，否则归入ψ₄**
4. 统计决定性基因树中各类型的比例

**关键工具：**
- **BUCKy**：贝叶斯方法，利用所有基因树的后验分布，通过统计收缩互相修正，可以提供置信区间；但计算规模限制了其在大数据集中的应用
- **IQ-TREE**：要求分类群单系，计算效率高，是当前最主流的gCF计算工具
**优势：**
- **唯一能估计ψ₄的方法，提供最完整的拓扑异质性视图**
- 可以扩展一致性向量超过四个条目，捕捉更丰富的不一致信息
- 是计算IC（节点确定性）和ICA（全节点确定性）的基础，**同时这两种方法受到基因树估计误差的影响较小**
**劣势：**
- **基因树估计误差**是最大瓶颈：短的单基因对齐容易导致错误的基因树推断
    - 低度误差：ψ₁被低估，ψ₂和ψ₃被高估
    - 高度误差：大量基因树落入ψ₄，掩盖真实的生物学信号
- **"并接效应"（concatalescence）**：当基因座跨越重组断点时，推断出的基因树倾向于反映最常见拓扑，导致ψ₁被高估

##### 方法二：四分类群一致性因子（qCF）
**输入：** 每个基因座的完整基因树，从中提取四元组（quartets）
**计算原理：**
1. **第一步——提取四元组：** 对每个基因座的基因树，针对感兴趣的内部分支，从A、B、C、D各类群中抽取代表性分类单元，构成四分类群的无根树（quartet）
2. **第二步——统计比例：** 统计所有相关四元组中属于ψ₁、ψ₂、ψ₃的比例（注意：无根四元组只有三种可能拓扑，因此ψ₄始终为0）

**关键特性：**
- 由于无根四元组只有三种拓扑，qCF**强制ψ₄=0**
- 即使某棵完整基因树中有分类群不是单系，仍然可以从中提取大量有效的四元组。
- 这使**qCF对基因树估计误差具有更强的鲁棒性**
- 
**主要工具：ASTRAL**
- ASTRAL通过最大化所有基因树中与物种树一致的四元组比例来推断物种树
- qCF是ASTRAL的标准输出之一
- ASTRAL利用qCV计算局部后验概率作为支持度度量

**优势：**
- **对基因树估计误差的鲁棒性远强于gCF**
- 计算高效，适合大规模数据集
- 是多种物种树推断和渐渗检测方法的基础
- 在多种溯祖模型下具有统计一致性

**劣势：**
- 强制ψ₄=0会导致当真实ψ₄>0时，ψ₁、ψ₂、ψ₃**被系统高估**
- 在基因树估计误差极高的情况下，即便真实的CF很低，qCF也会被"强迫"给出相对较高的值，**容易高估**
- 仍然依赖于先行估计的基因树，因此受到并接效应等问题的影响
  
##### 方法三：位点一致性因子（sCF）
**输入：** 基因组的长连接对齐
**计算原理：**
1. 识别"决定性位点"：仅保留对感兴趣分支具有信息量的位点（对于四分类群，决定性位点是在A、B、C、D四组中各有代表性碱基且能区分三种拓扑的位点）
2. 对每个决定性位点，基于位点模式判断其支持ψ₁、ψ₂还是ψ₃
3. 统计各类型决定性位点的比例
**两个版本：**
- **基于简约的sCF**：直接计数支持各拓扑的简约信息位点
- **基于似然的sCF**：利用似然框架减少同质性的影响，更为准确
**理论期望值：**
sCF受内部分支长度的**双重影响**：既影响基因树频率，又影响每棵基因树中内部分支的长度。因此sCF系统性地高于gCF。
![|575](../imag/@The%20meaning%20and%20measure%20of%20concordance%20factors%20in%20phylogenomics_/file-20260517133239791.png)
**优势：**
- **无需将对齐切分为短的不重组基因座**，避免了基因座划分带来的误差
- 可以直接从长的连接对齐计算，适用于基因座边界不明确的情况
- 与qCF相似，假设ψ₄=0，对非单系问题有一定鲁棒性
**劣势：**
- **同质性（homoplasy）问题**：单个位点可能因为多次替换而产生误导性的位点模式，导致**高估不一致性**（sCF低于真实值）
- 假设ψ₄=0，与qCF有相同的偏差问题
- 测量的是"支持各拓扑的位点比例"，而非"具有各拓扑的基因组比例"

### 3.5 鸟类演化的具体例子
![](../imag/@The%20meaning%20and%20measure%20of%20concordance%20factors%20in%20phylogenomics_/file-20260517140602324.png)

针对这个此研究，我们可以得到如下结论：
- 观察到的支持度与一致性之间的差异非常重要,它有助于理解每个支系在多大程度上关系到鸟类基因与性状历史的认知
- 不同支系间的CF值天差地别，反映了他们各自对于预测基因演化历史的能力
- 不同的CF计算方法可能会导致不同的结果，例如qcf与scf由于默认ψ₄=0，导致其ψ₁最小也有33%左右，而gcf就可以无限制的小
- Columbaves中观察到在gcf的计算下，主要拓扑的占比居然低于次要拓扑。目前的解释是认为其收到非单系以及计算方法的影响导致的误差，认为在这种情况下，可能qcf和scf的解释度要更高一些


# 4. 讨论与建议

**（1）进化性状分析中的指导作用**
对于CFs极低的分支应：
- 慎重解读基于物种树的特征演化推断（如祖先状态重建、进化速率估计）
- 认识到表型性状的共享特征可能反映的是不完全谱系分选或渐渗，而非共同祖先的同源性

**（2）外类群选择的指导**
一个理想的外类群在所有基因树中都应位于内类群之外（CF=100%），而高统计支持度并不保证这一点。如果相当比例的基因树显示外类群落在内类群之内，则基于该外类群的根置和特征极性化分析将产生偏差。

**（3）测试渐渗的统计框架**
在ILS纯模型下，ψ₂=ψ₃是一个可检验的假说。ABBA-BABA检验（D检验）实质上就是在检验位点水平上这一等式是否成立。四元组方法（如Δ统计量）则是在基因树层面做类似检验。ψ₂≠ψ₃提示存在渐渗。

**（4）渐渗网络和平行基因组的分析**
随着越来越多的研究发现物种树只是对复杂历史的近似，CFs可以作为判断物种树适用性的先验指标：当CFs很高时，物种树是基因组历史的良好代理；当CFs很低时，单一物种树可能需要被物种网络（species network）取代。

