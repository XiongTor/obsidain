---
title: 02-1-treePL流程简介
date: 2026-11-27
authors: Tao Xiong    Gemini 4 Neno
tags:
---
### 参考教程
[DeepWiki_treePL](https://deepwiki.com/blackrim/treePL/3.2-example-workflows)

### GitHub
[GitHub_treePL](https://github.com/sunray1/treepl)
### 自动化流程
[AUTO-MCMCTREE](https://github.com/SunLab-MiaoPu/PhyloerKit)

# 一、基本原理

## 1. 罚分似然法（Penalized Likelihood, PL）
treePL（Smith & O'Meara, 2012）是基于罚分似然框架开发的快速分子钟定年工具，专门用于处理大规模系统发育树（数百至数万个物种）。其核心思想是在泊松近似似然与相邻分支速率变化惩罚之间寻找平衡，最大化如下罚分目标函数：

$$\Psi = \ln L(t, r \mid b, N) - \lambda \cdot \Phi(r)$$

其中：
- $b$ 为输入分子树（phylogram）的枝长，$N$ 为建树所用的总位点数（`numsites`），两者乘积代表各枝的期望替换数；
- $\ln L$ 衡量当前节点时间 $t$ 与分支速率 $r$ 对观测枝长的拟合程度；
- $\Phi(r)$ 为速率粗糙度惩罚项（衡量父枝与子枝间速率差异，支持默认加法惩罚或 `log_pen` 对数惩罚）；
- $\lambda$ 为平滑参数（`smooth`），决定模型在“允许各枝自由变速”与“约束全树接近严格分子钟”之间的权衡。

## 2. 平滑参数选择与硬边界标定
- **交叉验证（Cross-Validation, CV）**：由于 $\lambda$ 无法直接通过最大似然估计，treePL 采用留一法（`cv`）或随机子采样（`randomcv`）逐次移除末端分支，用其余分支预测该分支长度并计算预测误差（`chisq`）。误差最低处对应的 $\lambda^*$ 即为最优平滑参数。==🔴即用来寻找最佳平滑参数smooth值==
- **Prime-basic analysis（优化器探测）** 解决计算效率与收敛问题 ——==🔴为树挑选最合适的数值优化算法==
- **硬边界约束（Hard bounds）**：与 MCMCTree 的软边界不同，treePL 的化石标定（`min` / `max`）为严格硬边界，优化后的节点年龄绝不会越出设定区间。
> 理论上来讲，cv和prime应该是不分先后的，但是现在默认先进行prime，将prime探寻出的最佳参数直接喂给cv然后让cv去寻找最佳smooth值。
![](../../../imag/02-1-treePL流程简介/file-20261127202951444.png)

![|720](../../../imag/02-1-treePL流程简介/treepl_principle.png)

# 二、标准分析流程

```mermaid
flowchart LR
    A["输入准备<br/>有根带枝长树 .tre<br/>化石标定配置 .config"] --> B["Step 1: prime<br/>探测最优优化器参数<br/>opt / optad / optcvad"]
    B --> C["Step 2: cv / randomcv<br/>扫描平滑参数 smooth<br/>取 chisq 最小值为最优值"]
    C --> D["Step 3: 正式定年<br/>代入 prime 参数与最优 smooth<br/>输出时间树与 .r8s 速率树"]
    A --> E["Bootstrap 置信区间流程<br/>RAxML -f j 生成 100 组重采样矩阵<br/>RAxML -f e 固定拓扑重估枝长并置根"]
    E --> F["对 100 棵树批量 treePL 定年<br/>cat 合并并用 TreeAnnotator 汇总 95% CI"]
```

## 1. 输入文件与核心配置参数
- **有根带枝长树（`treefile`）**：必须为带有分子替换枝长的 Newick 树（通常由 RAxML / IQ-TREE 估算，或物种树重估枝长获得），且已正确置根。
- **配置文件（`*.config`）**：核心参数说明如下：

| 参数                                  | 示例                        | 说明                                                 |
| :---------------------------------- | :------------------------ | :------------------------------------------------- |
| `treefile` / `outfile`              | `input.tre` / `dated.tre` | 输入带枝长有根树路径；输出时间树路径（同时生成 `.r8s` 速率树）                |
| `numsites`                          | `67367`                   | 用于估算枝长的比对总位点数                                      |
| `mrca` / `min` / `max`              | `mrca = nodeA sp1 sp2`    | 通过两个后代类群定义 MRCA，并用 `min` / `max` 设定年龄上下限（通常单位为 Ma） |
| `prime`                             | `prime`                   | 预测试各优化算法组合，输出推荐的 `opt`、`optad`、`optcvad` 等参数       |
| `cv` / `randomcv`                   | `cv`                      | 执行交叉验证；大树建议使用 `randomcv` 加速                        |
| `cvstart` / `cvstop` / `cvmultstep` | `10000` / `0.001` / `0.1` | CV 扫描平滑参数的起止范围与倍增步长，结果写入 `cvoutfile`               |
| `smooth`                            | `1`                       | 正式定年时填入 CV 找到的最优平滑参数                               |
| `thorough`                          | `thorough`                | 要求优化运行至收敛阈值，避免迭代过早终止                               |
| `log_pen`                           | `log_pen`                 | 可选，切换为对数惩罚函数（替代默认加法惩罚）                             |
| `nthreads`                          | `8`                       | 并行线程数                                              |

## 2. 单树标准三步法与 Bootstrap 置信区间流程
- **单树定年（点估计）**：
  1. **Step 1（`prime`）**：配置文件开启 `prime` 和 `thorough`，运行 `treePL prime.cfg > prime.log 2>&1`，提取日志中 `PLACE THE LINES BELOW IN THE CONFIGURATION FILE` 之后的参数行。
  2. **Step 2（`cv`）**：将 `prime` 参数写入配置，注释 `prime` 并开启 `cv`（或 `randomcv`）与 `cvstart` / `cvstop` / `cvmultstep`，从 `cvoutfile` 中选取 `chisq` 最小的 `smooth`。
  3. **Step 3（正式定年）**：填入最优 `smooth` 与 `prime` 参数，去掉 `cv`，指定 `outfile` 运行，得到最终时间树和对应的 `.r8s` 分支速率树。
- **获取 95% 置信区间（CI）**：
  - 单次 treePL 不输出置信区间。发表级分析通常先用 `raxmlHPC -f j` 生成 100 组 bootstrap 比对，再用 `raxmlHPC -f e -t <固定拓扑>` 估算 100 棵枝长树并统一置根，随后对每棵树分别跑 treePL 定年，最后 `cat` 合并 100 棵时间树并用 `TreeAnnotator` 汇总各节点的 95% CI

### 3. 运行
配置好config文件之后，直接运行即可
```
treePL TREEPL.config
```

**prime**，或者说basic dating analysis的config文件示例如下：
```
treefile = ../Fab.patol.353species.br.rt2024.ed.tre
smooth = 1
numsites = 271036
seed = 97867

[Fabaceae]
# F1 crown of Fabaceae: 70-65.35 Ma
mrca = Fabaceae_crown Sigmoidotropis_speciosa_ERR7619136 Cercis_canadensis_RKFX
min = Fabaceae_crown 65.35
max = Fabaceae_crown 70

#cvoutfile = Fabaceae_cv6.txt
nthreads = 10
thorough
prime

# 检查是否有祖先节点的 min 小于子孙节点的 min年龄，如果有打印警告并停止运行
checkconstraints
```

**cv**的config文件示例如下：
```
treefile = ../Fab.patol.353species.br.rt2024.ed.tre
smooth = 1
numsites = 271036
seed = 97867

[Fabaceae]
# F1 crown of Fabaceae: 70-65.35 Ma
mrca = Fabaceae_crown Sigmoidotropis_speciosa_ERR7619136 Cercis_canadensis_RKFX
min = Fabaceae_crown 65.35
max = Fabaceae_crown 70

cvoutfile = Fabaceae_cv6.txt
nthreads = 10

#run "cross-validation" to report the best smooth value for dating (the lowest chi-square value).

cv
thorough

# 下面的参数需要填入prime后生成的结果，这里只是作为示例
opt = 2
moredetail
optad = 2
moredetailad
optcvad = 1
moredetailcvad
```

**date**的config文件示例如下：
```
treefile = ../Fab.patol.353species.br.rt2024.ed.tre
smooth = 1  # 做完cv之后需要更新最合适的值
numsites = 271036
seed = 97867

[Fabaceae]
# F1 crown of Fabaceae: 70-65.35 Ma
mrca = Fabaceae_crown Sigmoidotropis_speciosa_ERR7619136 Cercis_canadensis_RKFX
min = Fabaceae_crown 65.35
max = Fabaceae_crown 70

date

# 下面的参数需要填入prime后生成的结果，这里只是作为示例
opt = 2
moredetail
optad = 2
moredetailad
optcvad = 1
moredetailcvad

outfile = Fabaceae_date6.txt
```
# 三、操作注意事项

1. **输入树枝长与拓扑检查**
   - 输入树必须带有分子替换枝长（与 MCMCTree 相反）且不能包含负枝长；极短枝或零长枝容易导致优化困难或局部速率异常，建树阶段应避免过度缺失数据或异常旁系同源序列拉长枝长。
   - 至少需要一个最大年龄约束（通常设在根节点）以锚定全树时间尺度，避免节点年龄无限漂移。

2. **正确把握 `prime` 与 `cv` 的顺序**
   - 官方文档（Stephen Smith / Maurin 2020）推荐 **`prime → cv → dating`**，使 `prime` 输出的 `optcvad` 与 `moredetailcvad` 在 CV 阶段即生效。部分课题组脚本采用 `cv → prime → dating`，在中小规模树上通常也能得到合理结果，但需确认 CV 曲线是否存在清晰的单一谷底。

3. **CV 搜索范围与细化验证**
   - 若最优 `smooth` 落在默认搜索区间（`1000`–`0.1`）的两端边界，说明真实最优点可能超出范围，必须外扩 `cvstart` 或缩小 `cvstop` 重跑；找到大致数量级后，可在谷底附近缩小步长（如 `cvmultstep = 0.5`）复核结果稳定性。
