### <font style="color:rgb(25, 27, 31);">14.</font><font style="color:rgba(0, 0, 0, 0.9);">基因组分析 | Inpactor2：基于深度学习进行 LTR 转座子的鉴定与分类：</font>[https://github.com/simonorozcoarias/Inpactor2](https://github.com/simonorozcoarias/Inpactor2)
<font style="color:rgb(0, 0, 0);">LTR 转座子在基因组中广泛存在，并且由于其结构定义相对标准（与 LINE , SINE 之类相比），因此更有利于对其的研究。同时，LTR 作为反转录转座子，会对基因组的结构和基因结构产生重要影响，因此对其进行鉴定与分类也至关重要。</font>

<font style="color:rgb(0, 0, 0);">另外，LTR 侧翼序列的比对还可计算 LTR 的插入时间。</font>

<font style="color:rgb(0, 0, 0);">根据 Nature Review 论文《A unified classification system for eukaryotic transposable elements》的定义，LTR 包括以下类别：</font>

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2025/png/54922767/1759028454880-3fd38aa6-e2c8-4985-a57e-a227b796a4ad.png)<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2025/png/54922767/1759028467405-e9a4f0af-5106-4061-8f15-625a7c5b9124.png)<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2025/png/54922767/1759028477219-86310d99-cf46-496f-8d49-2a69ba37acf7.png)

**<font style="color:rgb(89, 89, 89);">多软件比对结果</font>**

**<font style="color:rgb(239, 112, 96);">Inpactor2</font>**<font style="color:rgb(0, 0, 0);"> 具有显著的速度优势，且与 EDTA、LTR_FINDER、Inpactor1、TEsorter 和 TransposonUltimate 等相比，具有更高的特异性和精确度，以及更低的 FDR。</font>

```bash
#安装
git clone https://github.com/simonorozcoarias/Inpactor2.git
conda env create -f Inpactor2/Inpactor2.yml -y
conda activate Inpactor2
python3 /data/xueqin/software/Inpactor2/Inpactor2.py

#运行
/data/xueqin/Prunus/genome_assembly/1Chromosome/06_polish/24_Inpactor2_LTR
mkdir Prunus_mahaleb
nohup python3 /data/xueqin/software/Inpactor2/Inpactor2.py -f Prunus_mahaleb.fasta -o Prunus_mahaleb &

```

**Inpactor2_anno_summary.txt：**

** LTR 元素数量多，覆盖比例约 14.5%  **

Total Sequences: 8

Total Length: 271763850 bp

Class                  Count        bpMasked    %masked

=====                  =====        ========     =======

**LTR/RLC **               --           --           --   

```plain
RLC/ALE-RETROFIT   7920         5253523      1.93% 

RLC/BIANCA         1317         471080       0.17% 

RLC/IVANA-ORYCO    4005         2633810      0.97% 

RLC/SIRE           1248         1248707      0.46% 

RLC/TAR-TORK       6350         3546181      1.30% 
```

**LTR/RLG**                --           --           --   

```plain
RLG/ATHILA         7296         3624974      1.33% 

RLG/CRM            4733         2948920      1.09% 

RLG/GALADRIEL      208          176493       0.06% 

RLG/REINA          692          480480       0.18% 

**RLG/TAT            20817        14264507     5.25% **

RLG/TEKAY-DEL      12424        4733396      1.74% 

                  ---------------------------------

total interspersed 67010        39382071     **14.49%**
```

---

Total                  67010        39382071     14.49%



```bash
#统计所有物种的LTR情况
bash collect_LTR_summary_csv.sh > LTR_summary_table.csv

#!/bin/bash
# 用法: bash collect_LTR_summary_csv.sh > LTR_summary_table.csv

# 输出 CSV 表头
echo "Species,LTR_RLC_Count,LTR_RLC_bpMasked,LTR_RLC_masked,LTR_RLG_Count,LTR_RLG_bpMasked,LTR_RLG_masked,Total_Count,Total_bpMasked,Total_masked"

# 遍历每个目录
for dir in */; do
    summary="${dir%/}/Inpactor2_anno_summary.txt"
    if [ -f "$summary" ]; then
        species=${dir%/}

        # 提取 LTR/RLC 部分
        RLC_vals=$(grep "RLC/" "$summary" | awk '{c+=$2; b+=$3; p+=$4} END{if (NR>0) printf "%d,%d,%.2f", c,b,p; else print "0,0,0"}')
        if [ -z "$RLC_vals" ]; then RLC_vals="0,0,0"; fi

        # 提取 LTR/RLG 部分
        RLG_vals=$(grep "RLG/" "$summary" | awk '{c+=$2; b+=$3; p+=$4} END{if (NR>0) printf "%d,%d,%.2f", c,b,p; else print "0,0,0"}')
        if [ -z "$RLG_vals" ]; then RLG_vals="0,0,0"; fi

        # 提取 Total 行（一般在文件末尾）
        TOTAL_line=$(grep -E "Total[[:space:]]+[0-9]" "$summary" | tail -n 1)
        if [ -n "$TOTAL_line" ]; then
            TOTAL_vals=$(echo "$TOTAL_line" | awk '{printf "%d,%d,%.2f", $2,$3,$4}')
        else
            TOTAL_vals="0,0,0"
        fi

        # 输出结果
        echo "${species},${RLC_vals},${RLG_vals},${TOTAL_vals}"
    fi
done

```

| Species | LTR_RLC_Count | LTR_RLC_bpMasked | LTR_RLC_masked | LTR_RLG_Count | LTR_RLG_bpMasked | LTR_RLG_masked | Total_Count | Total_bpMasked | Total_masked |
| --- | --- | --- | --- | --- | --- | --- | --- | --- | --- |
| <font style="color:#000000;">Amborella_trichopoda</font> | <font style="color:#000000;">47458</font> | <font style="color:#000000;">34322447</font> | <font style="color:#000000;">4.7</font> | <font style="color:#000000;">211985</font> | <font style="color:#000000;">161716327</font> | <font style="color:#000000;">22.12</font> | <font style="color:#000000;">259443</font> | <font style="color:#000000;">196038774</font> | <font style="color:#000000;">26.81</font> |
| <font style="color:#000000;">Arabidopsis_thaliana</font> | <font style="color:#000000;">1380</font> | <font style="color:#000000;">1236043</font> | <font style="color:#000000;">1.02</font> | <font style="color:#000000;">3968</font> | <font style="color:#000000;">5093666</font> | <font style="color:#000000;">4.27</font> | <font style="color:#000000;">5348</font> | <font style="color:#000000;">6329709</font> | <font style="color:#000000;">5.29</font> |
| <font style="color:#000000;">DYGY</font> | <font style="color:#000000;">134155</font> | <font style="color:#000000;">104228081</font> | <font style="color:#000000;">11.44</font> | <font style="color:#000000;">359196</font> | <font style="color:#000000;">440172873</font> | <font style="color:#000000;">48.3</font> | <font style="color:#000000;">493351</font> | <font style="color:#000000;">544400954</font> | <font style="color:#000000;">59.74</font> |
| <font style="color:#000000;">Prunus_avium</font> | <font style="color:#000000;">38631</font> | <font style="color:#000000;">22951631</font> | <font style="color:#000000;">6.66</font> | <font style="color:#000000;">77625</font> | <font style="color:#000000;">119336258</font> | <font style="color:#000000;">34.65</font> | <font style="color:#000000;">116256</font> | <font style="color:#000000;">142287889</font> | <font style="color:#000000;">41.32</font> |
| <font style="color:#000000;">Prunus_campanulata</font> | <font style="color:#000000;">39156</font> | <font style="color:#000000;">19188995</font> | <font style="color:#000000;">6.83</font> | <font style="color:#000000;">61510</font> | <font style="color:#000000;">43744302</font> | <font style="color:#000000;">15.56</font> | <font style="color:#000000;">100666</font> | <font style="color:#000000;">62933297</font> | <font style="color:#000000;">22.39</font> |
| <font style="color:#000000;">Prunus_humilis</font> | <font style="color:#000000;">24343</font> | <font style="color:#000000;">11701965</font> | <font style="color:#000000;">4.61</font> | <font style="color:#000000;">38492</font> | <font style="color:#000000;">25114980</font> | <font style="color:#000000;">9.92</font> | <font style="color:#000000;">62835</font> | <font style="color:#000000;">36816945</font> | <font style="color:#000000;">14.52</font> |
| <font style="color:#000000;">Prunus_mandshurica</font> | <font style="color:#000000;">16903</font> | <font style="color:#000000;">9391632</font> | <font style="color:#000000;">4.2</font> | <font style="color:#000000;">36142</font> | <font style="color:#000000;">22339164</font> | <font style="color:#000000;">9.99</font> | <font style="color:#000000;">53045</font> | <font style="color:#000000;">31730796</font> | <font style="color:#000000;">14.19</font> |
| <font style="color:#000000;">Prunus_mira</font> | <font style="color:#000000;">33498</font> | <font style="color:#000000;">19991292</font> | <font style="color:#000000;">7.93</font> | <font style="color:#000000;">45544</font> | <font style="color:#000000;">26335891</font> | <font style="color:#000000;">10.46</font> | <font style="color:#000000;">79042</font> | <font style="color:#000000;">46327183</font> | <font style="color:#000000;">18.39</font> |
| <font style="color:#000000;">Prunus_padus</font> | <font style="color:#000000;">69032</font> | <font style="color:#000000;">32605016</font> | <font style="color:#000000;">7.18</font> | <font style="color:#000000;">115526</font> | <font style="color:#000000;">80179675</font> | <font style="color:#000000;">17.63</font> | <font style="color:#000000;">184558</font> | <font style="color:#000000;">112784691</font> | <font style="color:#000000;">24.79</font> |
| <font style="color:#000000;">Prunus_persica</font> | <font style="color:#000000;">30316</font> | <font style="color:#000000;">16770866</font> | <font style="color:#000000;">7.34</font> | <font style="color:#000000;">32682</font> | <font style="color:#000000;">26176927</font> | <font style="color:#000000;">11.44</font> | <font style="color:#000000;">62998</font> | <font style="color:#000000;">42947793</font> | <font style="color:#000000;">18.77</font> |
| <font style="color:#000000;">Prunus_salicina</font> | <font style="color:#000000;">30766</font> | <font style="color:#000000;">18578825</font> | <font style="color:#000000;">6.54</font> | <font style="color:#000000;">68576</font> | <font style="color:#000000;">59610309</font> | <font style="color:#000000;">20.98</font> | <font style="color:#000000;">99342</font> | <font style="color:#000000;">78189134</font> | <font style="color:#000000;">27.52</font> |
| <font style="color:#000000;">Prunus_zhengheensis</font> | <font style="color:#000000;">18316</font> | <font style="color:#000000;">8701558</font> | <font style="color:#000000;">3.7</font> | <font style="color:#000000;">39423</font> | <font style="color:#000000;">37805924</font> | <font style="color:#000000;">16.08</font> | <font style="color:#000000;">57739</font> | <font style="color:#000000;">46507482</font> | <font style="color:#000000;">19.79</font> |
| <font style="color:#000000;">Rosa_chinensis</font> | <font style="color:#000000;">178508</font> | <font style="color:#000000;">106184057</font> | <font style="color:#000000;">20.59</font> | <font style="color:#000000;">148695</font> | <font style="color:#000000;">111313893</font> | <font style="color:#000000;">21.59</font> | <font style="color:#000000;">327203</font> | <font style="color:#000000;">217497950</font> | <font style="color:#000000;">42.18</font> |


```bash
library(tidyverse)

data <- read.csv("LTR_summary_table.csv", header = TRUE)

data_long <- data %>%
  pivot_longer(cols = c(LTR_RLC_masked, LTR_RLG_masked),
               names_to = "Class", values_to = "Masked")
my_order <- c("Arabidopsis_thaliana","Vitis_vinifera","Rosa_chinensis","DYGY","Prunus_padus","Prunus_avium","Prunus_campanulata", "Prunus_mira","Prunus_persica","Prunus_humilis","Prunus_mandshurica","Prunus_salicina","Prunus_zhengheensis")
data_long$Species <- factor(data_long$Species, levels = my_order)
data_long <- data %>%
  pivot_longer(cols = c(LTR_RLC_masked, LTR_RLG_masked),
               names_to = "Class", values_to = "Masked") %>%
  mutate(Class = case_when(
    Class == "LTR_RLC_masked" ~ "RLC",
    Class == "LTR_RLG_masked" ~ "RLG"
  ))

pdf("Inpactor2_LTR.pdf", height = 6, width = 12)
ggplot(data_long, aes(x = Species, y = Masked, fill = Class)) +
  geom_bar(stat = "identity", position = "stack", width = 0.5) +
  coord_flip() +
  scale_fill_manual(values = c("RLC" = "#66C2A5", "RLG" = "#FC8D62")) +
  theme_minimal(base_size = 14) +
  theme(
    panel.grid = element_blank(),
    panel.border = element_blank(),
    axis.line = element_line(color = "black"),
    axis.ticks = element_line(color = "black"),
    axis.text.y = element_text(face = "bold", size = 12),
    axis.text.x = element_text(size = 12),
    legend.title = element_blank(),
    legend.position = "right"
  ) +
  labs(
    y = "% Genome masked",
    x = "Species",
    title = "Proportion of LTR Types across Species"
  )

dev.off()
```

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2025/png/54922767/1759906453809-970170ec-b12a-4469-a7f6-b5e006920c76.png)

