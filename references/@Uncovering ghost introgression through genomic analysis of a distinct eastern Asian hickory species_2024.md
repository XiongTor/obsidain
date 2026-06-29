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

## 1. 文献研究类群与使用的数据集

**研究类群：** 胡桃科（Juglandaceae）山核桃属 *Carya*（17–19 种，东亚—北美间断分布），核心聚焦**喙核桃 *Carya sinensis***（beaked hickory，又名 *Annamocarya sinensis*，一种具"鸟喙状"果尖的残遗种，分布于中国西南与越南北部的低山热带林下）。外群为 *Pterocarya stenoptera*（枫杨）。

**数据组成：**

| 数据类型 | 内容 | 规模 |
|---|---|---|
| 新组装染色体水平参考基因组 | *C. sinensis*（623.16 Mb，contig N50 3.15 Mb，scaffold N50 38.85 Mb，16 条假染色体，QV 34.32，BUSCO 96.9%，LAI 19.1） | Illumina + PacBio + Hi-C，235.51 Gb 原始数据（≈3719） |
| 新组装染色体水平参考基因组 | *C. cathayensis*（山核桃，698.09 Mb，contig N50 2.59 Mb，scaffold N50 43.59 Mb，16 条假染色体，QV 38.16，BUSCO 97.1%，LAI 16.26） | 207.53 Gb 原始数据（≈2809） |
| 已发表参考基因组 | *C. illinoinensis*（薄壳山核桃，代表北美 NA 支系） | Lovell et al., 2021 |
| 全基因组重测序 | 43 个 *C. sinensis* 个体（覆盖其已知大部分分布区） | 309 期望深度，150 bp PE，得 24,751,236 双等位 SNP |
| 全基因组重测序 | 11 个个体代表 11 个二倍体 *Carya* 物种（6 EA + 5 NA） | 用于物种树与渐渗分析 |
| 物种树 SNP 数据集 | 13 个个体比对到 *P. stenoptera* 参考，去单例 + LD 过滤（>20 kb） | 19,556 个全基因组 SNP |
| 共识基因组序列 | 6 EA 物种比对到 *C. cathayensis*，5 NA 物种比对到 *C. illinoinensis* | 用于构建物种树与渐渗分析的基因序列 |

> 注：共覆盖 12 个二倍体 *Carya* 物种 + 外群 *P. stenoptera*，EA 支系包括 *C. sinensis, C. cathayensis, C. dabieshanensis, C. hunanensis, C. kweichowensis, C. poilanei, C. tonkinensis*；NA 支系包括 *C. aquatica, C. cordiformis, C. illinoinensis, C. laciniosa, C. ovata*。

## 2. 文献使用的主要方法

**基因组组装与注释：**
- 组装：Falcon v3.1（PacBio 预组装）→ Pilon v1.22（Illumina 抛光两轮）→ HiC-Pro v2.11.1 + LACHESIS（Hi-C 挂载到假染色体）；k-mer 法估 genome size
- 注释：PASA v2.1.0 + AUGUSTUS v3.2.3 + GeneWise v2.4.1，EVM v1.1.1 整合；Blast2GO / KAAS 注释 GO、KEGG；BUSCO embryophyta_odb10、LAI、QV 评估质量
- 共线性与 WGD：BLASTP + MCScanX 识别共线性基因对；KaKs_Calculator 2.0（Gamma-MYN）估 Ks，揭示胡桃科两次多倍化事件

**物种树重建（关键前提，须对基因流稳健）：**
- 4 物种（*C. sinensis, C. cathayensis, C. illinoinensis* + 外群）：ASTRAL-Pro（DNA 序列）、whole-genome microsynteny（Zhao et al., 2021）、local gene content（Pett et al., 2019）三法一致
- 12 物种扩展集：CVTree v4.0（全蛋白）、ASTRAL v5.7.4（7398 单拷贝基因）、SVDquartets（19,556 SNP）、PhyloNet（网络）
- **亮点**：选用 microsynteny / gene content 这类**基于基因组结构**的方法构建物种树，因其对基因流稳健，避免序列法在广泛渐渗下返回"网状史"而非"二歧史"

**渐渗检测（核心方法学创新）：**
- **D-statistic（ABBA–BABA，Patterson et al., 2012）**：对 30 个有根四分体 [((P1: *C. sinensis*, P2: 5 EA), P3: 6 NA), O: *P. stenoptera*] 检验；只能判断"有无渐渗"，**无法区分**非姐妹种间渐渗 vs 幽灵支系渐渗
- **BPP v4.6.2（MSci 模型，Flouri et al., 2020）**：全似然法，直接分析多位点序列（利用基因树拓扑+分支长度），比较 **6 种渐渗情景**（非姐妹渐渗、外群幽灵渐渗、内群幽灵渐渗等）；选 3 个代表性三元组用 log marginal likelihood 选最优模型；并估继承概率、渐渗时间与分化时间
- **PhyloNet**：12 物种全数据集推断系统发育网络，识别 *C. sinensis* 为网状节点，估继承概率 c
- **VolcanoFinder v1.0（Setter et al., 2020）**：仅用受体物种群体多态性数据（24,751,236 SNP / 43 个体），基于复合似然比（CLR）扫描**适应性渐渗**信号（可覆盖幽灵供体）；阈值 CLR > 100，20-kb 区块
- **Est-SFS（Keightley & Jackson, 2018）**：以 *C. tonkinensis*、*C. illinoinensis* 为外群极化祖先/衍生等位基因，验证候选基因的古老来源
- **分化时间**：BPP MSci / MSC 模型（考虑/不考虑渐渗）、BEAST2（基因串联树，作对比，会高估）

**变异检测：** BWA-MEM 比对 → SAMtools 过滤 → SENTIEON DNAseq（去重、indel 重比对、call SNP）/ BCFtools（共识基因组）

## 3. 文献的主要内容与理论

### 1）核心问题
- **幽灵渐渗（ghost introgression）**——来自已灭绝/未采样支系的遗传物质渗入现存物种——在植物中确凿证据稀缺，可能并非真罕见，而是**检测方法的局限/误读**所致（Tricou et al., 2022a 模拟显示多数显著 D 值其实源自幽灵支系）
- **D-statistic 的解释困境**：ABBA/BABA 过量既可由非姐妹种间渐渗引起，也可由幽灵支系渗入 P1/P2 引起；位点模式本身**无法区分**两种情景，需额外分析
- **物种树前提**：D-statistic 与 MSci 模型都要求有准确的**有根物种树**，但广泛渐渗会使基于序列的系统发育反映"网状史"而非"二歧史"——须用对基因流稳健的基因组结构信号
- **聚焦 *C. sinensis***：其分类地位长期争议（单型属 *Annamocarya* vs 归入 *Carya*），形态"原始/祖征"，疑有古老渐渗自未知近缘支系

### 2）主要结论

**（a）系统发育位置澄清：**
- 多方法（microsynteny、gene content、ASTRAL-Pro；扩展集 CVTree/ASTRAL/SVDquartets/PhyloNet）一致支持 **\*C. sinensis* 是东亚 EA 山核桃支系的姐妹种**，EA 与 NA 两支系互为姐妹
- 据此主张将喙核桃**归入 *Carya***（置于 Rhamphocarya 组），而非保留为单型属 *Annamocarya*

**（b）幽灵渐渗的三重证据链：**
1. **D-statistic**：30 个四分体**全部显著正值**，证明存在古老渐渗（但来源不明）
2. **BPP（MSci）**：6 种情景中，**外群幽灵渐渗模型（model 6）log marginal likelihood 最高**，3 个三元组结果一致——渐渗来自已灭绝的祖先山核桃支系
3. **PhyloNet**：*C. sinensis* 为网状节点，继承概率 c = 0.29（与 BPP 估的 0.22 吻合）
4. **VolcanoFinder**：检出 44 个 20-kb 区块、36 个候选适应性渐渗基因；GO 富集涉昼夜节律、红光/远红光信号、防御反应；其中 ASI034477.1（FHY3/FAR1 转录因子，远红光信号，助耐荫）与 ASI024295.1（RPP13-like 抗病蛋白）含祖先等位基因，提示古老来源

**（c）分化时间与生物地理（BPP MSci）：**
- 幽灵支系约 **5.42 Ma**（HPD 4.97–5.89）从山核桃共同祖先分化
- 渐渗入 *C. sinensis* 发生于约 **2.72 Ma**（HPD 2.24–3.03），继承概率 0.22
- 现存 EA–NA 分化约 **3.96 Ma**（HPD 3.87–4.05），EA 多样化 2.96 Ma，NA 多样化 2.01 Ma
- 不考虑渐渗的 MSC 模型得 3.74 Ma（相近）；BEAST2 基因串联树得 5.10 Ma（**高估，因序列分化早于物种形成**）

**（d）回迁假说（back-migration）：**
- 分化时间（3.96 Ma）远晚于白令陆桥首次关闭（~5.5 Ma），且 EA 山核桃具裸芽/非典型芽鳞（祖征）、NA 具覆瓦状芽鳞（温带适应衍征）
- 据此提出：**现存 NA 山核桃可能是由 EA 经白令陆桥回迁**，其在 NA 的祖先于中新世中期降温时灭绝

### 3）理论创新点
- **整合 D-statistic + BPP 的策略**：用 D-statistic 快速筛查"有无渐渗"，再用 BPP 全似然法（直接用序列，兼顾拓扑与分支长度）在多模型间比较，**精准鉴别渐渗类型**（非姐妹 vs 外群幽灵 vs 内群幽灵）——首次将该整合思路用于实证
- 强调**对基因流稳健的基因组结构信号（microsynteny / gene content）作为物种树先验**，是 BPP/PhyloNet 推断可靠的前提——这是推断幽灵渐渗信心提升的关键一步
- 提出 VolcanoFinder 检出的 36 基因中 20 个在其他近缘/外群物种无直系同源，可能正是**幽灵供体已灭绝**留下的基因组印记

### 4）与既有研究的关系
- 系统发育位置与早期基于少量分子标记/叶绿体基因组的研究（Manos et al., 2007; Zhang et al., 2013; Xi et al., 2022）一致，但**这些方法无法检测种间基因流**，故需用基因组数据 + 网络法重新验证
- 幽灵渐渗案例：动物中多由化石 DNA 证实（现代人、棕熊）；植物中无化石 DNA 的案例有 *Oxyria sinensis*、*Allium tetraploideum*、*Thuja sutchuenensis*、*Jaltomata*——本研究为**木本植物又一确证案例**
- 方法学上呼应 Pang & Zhang (2024)：推荐用 BPP 区分幽灵渐渗与传统 D-statistic 解释；呼应 Tricou et al. (2022a)：多数显著 D 值可能源自幽灵支系，须重新审视 D-statistic 的常规解读
- 生物地理上修正 Manchester (1987)/Zhang et al. (2013) 的"NA 起源→经白令陆桥入亚"经典情景，提出反向回迁假说

## 4. 思考

### （1）D-statistic 的解释边界与本研究的核心贡献
D-statistic 仅凭 ABBA/BABA 位点模式**只能判断渐渗有无，不能定来源**。本研究用 BPP 在 6 种明确渐渗模型间做似然比较，把"显著 D 值"细化为"外群幽灵渐渗"——这正是 Tricou et al. (2022a) 所呼吁的"重新审视 D-statistic"。对我的项目而言，若 QuIBL/D-statistic 检出显著渐渗，**不能直接断言现存非姐妹种间基因流**，应考虑幽灵支系/未采样类群的可能，必要时用 BPP MSci 做模型比较。

### （2）物种树的"基因流稳健"先验是渐渗推断的根基
D-statistic 与 MSci 模型都要求**准确的有根物种树**，否则结果无意义。本研究的精明之处在于：用 microsynteny / gene content（基因组结构信号，对基因流稳健）而非纯序列法建树，避免了"序列树=网状史"的陷阱。这提示在蔷薇科核质冲突分析中，**可考虑引入基因组结构/共线性信息**作为独立的物种树先验，以校验基于序列的 ASTRAL/ML 树。

### （3）BPP 的假设与局限
- 假设 JC69 突变模型 + 恒定分子钟 → 仅适用于近缘种
- 要求位点独立、无位点内重组（实际常违反，但 Zhu et al., 2022 表明现实重组率对 BPP 估分支长/渐渗概率影响很小）
- 即便单个二倍体个体/物种也能准确估渐渗时间与分化时间（Tiley et al., 2023）——对采样受限类群友好

### （4）分化时间方法的取舍
BEAST2 基因串联树（5.10 Ma）**系统性高估**，因序列分化先于物种形成；BPP MSC/MSci（3.74–3.96 Ma）更接近真实物种形成时间。**做分化时间估计时，串联法高估是预期偏差，溯祖法（BPP）更可靠**——这与我项目用 QuIBL 区分 ILS 与渐渗时关注"溯祖单位分支长度"的思路一致。

### （5）幽灵渐渗的适应性意义
VolcanoFinder 检出的 FHY3/FAR1（远红光信号、耐荫）与 RPP13（抗病）暗示幽灵渐渗可能赋予 *C. sinensis* 在热带低地林下弱光环境的适应性。说明**幽灵支系虽已灭绝，其适应性等位基因可在受体物种中留存并发挥作用**——渐渗不仅是系统发育"噪声"，更是适应性演化的来源。

### （6）PhyloNet 网络边解释的谨慎
PhyloNet 无法指明哪条亲本边对应渐渗，惯例取权重高者为物种形成边、低者为渐渗边；但渐渗可影响基因组大部分区域，**网络边的"物种形成 vs 渐渗"归属需谨慎**，最好用 BPP 的模型比较交叉验证（本研究正是这样做的）。

![](../imag/@Uncovering%20ghost%20introgression%20through%20genomic%20analysis%20of%20a%20distinct%20eastern%20Asian%20hickory%20species_2024/)
