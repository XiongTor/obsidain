### 16.<font style="color:rgba(0, 0, 0, 0.9);">ModDotPlot：让基因组重复序列分析变得如此直观</font>
学习链接：[https://mp.weixin.qq.com/s/WFlCUR4TX7H8yjjSbxYTOA](https://mp.weixin.qq.com/s/WFlCUR4TX7H8yjjSbxYTOA)、[https://mp.weixin.qq.com/s/5FYgxQ4KFLgNjtuf4ax5lg](https://mp.weixin.qq.com/s/5FYgxQ4KFLgNjtuf4ax5lg)、[https://mp.weixin.qq.com/s/VAxK6RewAZWCsxBVS2wHfg](https://mp.weixin.qq.com/s/VAxK6RewAZWCsxBVS2wHfg)

<font style="color:rgba(0, 0, 0, 0.9);">在youtube上也有介绍这个工具的视频</font>：[https://www.youtube.com/watch?v=_7sQaljB_ys&t=2151s](https://www.youtube.com/watch?v=_7sQaljB_ys&t=2151s)

**ModDotPlot** 是一个用于 **串联重复序列（tandem repeats）和基因组比对结果的点阵图可视化工具**。  
它可以用于从短序列到整条基因组的可视化分析，是早期工具 **StainedGlass** 的继任者。

<font style="color:rgb(63, 63, 63);">其核心算法通过将输入序列分割为多个 </font>**由 k-mer（k 碱基片段）草图（modimizers）**<font style="color:rgb(63, 63, 63);"> 组成的区间，从而快速近似计算这些区间之间的 </font>**平均核苷酸相似度（Average Nucleotide Identity, ANI）**<font style="color:rgb(63, 63, 63);">。这意味着它能非常高效地可视化基因组内部的重复、反转、重排等结构特征。</font>

<font style="color:rgb(63, 63, 63);">在基因组研究中，重复序列是一个绕不开的话题：</font>

+ <font style="color:rgb(63, 63, 63);">• </font><font style="color:rgb(63, 63, 63);">🔁</font><font style="color:rgb(63, 63, 63);"> </font>**<font style="color:rgb(15, 76, 129);">串联重复</font>**<font style="color:rgb(63, 63, 63);">：如卫星DNA、微卫星等，与疾病和进化密切相关</font>
+ <font style="color:rgb(63, 63, 63);">• </font><font style="color:rgb(63, 63, 63);">🧬</font><font style="color:rgb(63, 63, 63);"> </font>**<font style="color:rgb(15, 76, 129);">转座子</font>**<font style="color:rgb(63, 63, 63);">：基因组中的"跳跃基因"，影响基因表达</font>
+ <font style="color:rgb(63, 63, 63);">• </font><font style="color:rgb(63, 63, 63);">📊</font><font style="color:rgb(63, 63, 63);"> </font>**<font style="color:rgb(15, 76, 129);">结构变异</font>**<font style="color:rgb(63, 63, 63);">：重复序列的扩增或缺失可能导致疾病</font>
+ <font style="color:rgb(63, 63, 63);">• </font><font style="color:rgb(63, 63, 63);">🎯</font><font style="color:rgb(63, 63, 63);"> </font>**<font style="color:rgb(15, 76, 129);">进化研究</font>**<font style="color:rgb(63, 63, 63);">：重复序列的模式反映物种进化历史</font>

**<font style="color:rgb(15, 76, 129);">传统分析方法的痛点</font>**<font style="color:rgb(63, 63, 63);">：</font>

+ <font style="color:rgb(63, 63, 63);">• 现有工具要么太慢，要么功能有限</font>
+ <font style="color:rgb(63, 63, 63);">• 缺乏交互式可视化能力</font>
+ <font style="color:rgb(63, 63, 63);">• 难以处理大规模基因组数据</font>
+ <font style="color:rgb(63, 63, 63);">• 重复序列模式不够直观</font>

<font style="color:rgb(63, 63, 63);">ModDotPlot是StainedGlass工具的精神继承者，专门为解决这些问题而设计。它使用了一种创新的</font>**<font style="color:rgb(15, 76, 129);">modimizer</font>**<font style="color:rgb(63, 63, 63);">算法，能够快速近似计算序列间的平均核苷酸相似性。</font>

```bash
#安装
git clone https://github.com/marbl/ModDotPlot.git
cd ModDotPlot
python -m venv venv
source venv/bin/activate
python -m pip install .

#路径：/data/xueqin/Prunus/genome_assembly/1Chromosome/06_polish/27_ModDotPlot
#Python 自带的虚拟环境（venv）
conda activate base
source venv/bin/activate

ln -s /data/xueqin/Prunus/genome_assembly/1Chromosome/06_polish/04.assessment/LAI/T2T_only_chromosome/T2T_polish_filtered_output_seqkit.fa ./
seqkit fx2tab -n T2T_polish_filtered_output_seqkit.fa
seqkit fx2tab -n T2T_polish_filtered_output_seqkit.fa > chr_list.txt
mkdir -p ModDotPlot_output
nohup bash modotplot.sh &
#modotplot.sh：
#!/bin/bash
# 输入基因组 fasta
GENOME="T2T_polish_filtered_output_seqkit.fa"

# 输出目录
OUTDIR="ModDotPlot_output"
mkdir -p $OUTDIR

# k-mer, 分辨率, 最低序列同一性阈值
KMER=21
RESOLUTION=1000
IDENTITY=86

# 提取染色体列表
samtools faidx $GENOME
cut -f1 $GENOME.fai > chr_list.txt

# 循环处理每条染色体
while read CHR; do
    echo "Processing $CHR ..."
    
    # 临时 fasta 文件
    TMPFASTA="${CHR}.fa"
    samtools faidx $GENOME $CHR > $TMPFASTA

    # ModDotPlot 静态模式运行
    moddotplot static \
        -f $TMPFASTA \
        -o $OUTDIR/$CHR \
        -k $KMER \
        -r $RESOLUTION \
        -id $IDENTITY

    # 删除临时文件
    rm $TMPFASTA

    echo "$CHR done!"
done < chr_list.txt

#示例代码
moddotplot static -f genome.fa -o output_dir
输出内容包括：
.bedpe 文件：存储区间配对及相似度矩阵
_TRI 图：三角形自比对矩阵图
_FULL 图：完整矩阵图
.svg 和 .png 图像文件
相似度直方图
#• 点图：显示序列相似性
#• 三角形图：突出重复模式
#• 直方图：统计相似性分布
#• 注释轨道：叠加基因和重复序列信息

```

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2025/png/54922767/1759740740731-6cb07034-b107-45d4-8649-f9d6af9f4934.png)<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2025/png/54922767/1759740799137-6fc3d536-c979-4041-96d9-d496128a098c.png)<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2025/png/54922767/1759818076301-5e4cb887-f8e3-4e4e-a950-9347f8dcc773.png)

**自我对比：**

**Chr_1_FULL.png**



<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2025/png/54922767/1759816987875-625ab7a8-b962-417c-bba6-27a4a148d0fd.png)

<font style="color:rgb(63, 63, 63);">ModDotPlot比较分析结果。显示chr1和chr1染色体</font>序列相似性<font style="color:rgb(63, 63, 63);">，红色区域表示高相似性，蓝色区域表示低相似性，白色区域表示无显著相似性。</font>  



**Chr_1_HIST.png**

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2025/png/54922767/1759817023968-3f1ba0ff-384c-4837-ad72-6cb8de99c189.png)

**Chr_1_TRI.png**

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2025/png/54922767/1759817053673-7140ba64-b734-4e0d-9a17-ab0cb2446af0.png)





<font style="color:#df2a3f;background-color:#fbde28;">下面模块还未分析</font>

