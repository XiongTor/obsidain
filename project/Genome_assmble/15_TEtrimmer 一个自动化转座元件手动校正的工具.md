### 15.<font style="color:rgba(0, 0, 0, 0.9);">TEtrimmer 一个自动化转座元件手动校正的工具</font>
参考链接：[https://mp.weixin.qq.com/s/HqK5PV-NPsw3WW1O_hGjbQ](https://mp.weixin.qq.com/s/HqK5PV-NPsw3WW1O_hGjbQ)

<font style="color:rgb(89, 89, 89);">转座元件（Transposable elements, TEs）是基因组中能够“跳跃”的重复DNA序列，曾被认为是“</font>垃圾DNA<font style="color:rgb(89, 89, 89);">”。然而，越来越多的研究表明，TEs在基因调控、物种适应和基因组进化中扮演着至关重要的角色。因此，对基因组中的TEs进行精准的识别和注释，是功能基因组学、比较基因组学和进化生物学研究的基础。然而，TE的注释工作极具挑战性，因为它们序列多样性高、经常以片段化或相互嵌套的形式存在。尽管已有</font>EDTA<font style="color:rgb(89, 89, 89);">和RepeatModeler2等自动化工具用于从头（</font>_<font style="color:rgb(89, 89, 89);">de novo</font>_<font style="color:rgb(89, 89, 89);">）识别TEs，但其输出的TE序列库往往包含大量错误和不完整的序列。目前，获得高质量TE序列库的“金标准”仍然依赖于耗时、费力且需要深厚专业知识的手动校正（manual curation）。这一过程已成为</font>基因组注释<font style="color:rgb(89, 89, 89);">流程中的主要瓶颈，严重限制了相关研究的通量和效率。</font>



<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2025/png/54922767/1759734858318-1f1074bf-e2d4-4b78-8504-aa5fe81e3e17.png)

<font style="color:rgb(89, 89, 89);">结果显示，TEtrimmer的加入显著提升了鉴定出完整TE（被定义为“Perfect”级别）的能力。在所有测试的基因组中，相比于原始工具，经过TEtrimmer优化的流程能够多鉴定出1.2至29.8倍的“Perfect”TE家族（图6）。特别是在DNA转座子和LINE元件的鉴定上，TEtrimmer表现尤为出色（图7）。即使在TE含量极高、注释难度极大的人类基因组中，TEtrimmer也能显著降低未分类TE的比例，并准确鉴定出大量SINE元件，而这是传统工具难以做到的（图8）。</font>

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2025/png/54922767/1759735039494-e3e2cc95-2a5b-485f-8253-6bbf5ba54b92.png)<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2025/png/54922767/1759735056569-2f215561-b124-42d6-b511-b7653789a439.png)<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2025/png/54922767/1759735071505-f9536d76-25c6-4d6a-aec8-8b94d9d0ecdd.png)

```bash
#运行
conda create -n TEtrimmer
conda create --name TEtrimmer python=3.10 samtools=1.22.1
conda activate TEtrimmer
#TEtrimmer 分析的作用
TEtrimmer 是用于 优化/裁剪转座元件 (TE) 库的工具，通常用于去除冗余序列、修正 TE 共识序列，并进行分类。
它依赖两类输入：
基因组序列（genome）
TE 共识序列库（TE consensus library）
基因组：ln -s /data/xueqin/Prunus/genome_assembly/1Chromosome/06_polish/04.assessment/LAI/T2T-Polish.fa ./
EDTA获得的TE 共识序列库：ln -s /data/xueqin/Prunus/genome_assembly/1Chromosome/06_polish/07.EDTA/T2T-Polish.fa.mod.EDTA.anno/T2T-Polish.fa.mod.EDTA.TElib.fa ./
#大叶桂樱所有数据
/data/xueqin/Prunus/genome_assembly/1Chromosome/06_polish/25_TEtrimmer_TE
TEtrimmer --input_file T2T-Polish.fa.mod.EDTA.TElib.fa \
          --genome_file T2T-Polish.fa \
          --output_dir ./ \
          --num_threads 20
          --classify_all


#只有染色体的
/data/xueqin/Prunus/genome_assembly/1Chromosome/06_polish/25_TEtrimmer_TE/T2T_only_chromosome
ln -s /data/xueqin/Prunus/genome_assembly/1Chromosome/06_polish/04.assessment/LAI/T2T_only_chromosome/T2T_polish_filtered_output_seqkit.fa ./
TEtrimmer --input_file ../T2T-Polish.fa.mod.EDTA.TElib.fa \
          --genome_file T2T_polish_filtered_output_seqkit.fa \
          --output_dir ./ \
          --num_threads 20
          --classify_all

```

### 
