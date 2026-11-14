### 6.Cicros图绘制
###### 前期文件准备
```bash
#路径：老服务器/data/xueqin/Project/Prunus/Prunus_Geome_analyses/Prunus_zippeliana_genome/Haphic_t2t/Circos_2025.7.10
#conda create -n circos -y
#conda install bioconda::circos
conda activate circos
#学习参考链接https://www.cnblogs.com/bgicollege/p/15543482.html、https://mp.weixin.qq.com/s/-je08Abc1S4OO5uYUfifIA
#第1步，准备数据
#基因组染色体序列
grep -r -p "^DYGYChr" T2T-Polish.fa.new.masked.fa >T2T-Polish.fa.new.masked_new.fa
grep -v "^scaffold_" DYGY.wgdi.gff>DYGY.wgdi_new.gff(/data/xueqin/Project/Prunus/Prunus_Geome_analyses/Prunus_zippeliana_genome/Haphic_t2t/DYGY_2025.6.30/DYGY.gff)

##准备核型文件
samtools faidx T2T-Polish.fa.new.masked_new.fa
cat T2T-Polish.fa.new.masked_new.fa.fai|awk '{print  "chr\t-\t"$1"\t"$1"\t0\t"$2"\tvlgrey"}'|grep Chr|sort -n -k 3  > first.circos

##准备基因密度文件
bedtools makewindows -g T2T-Polish.fa.new.masked_new.fa.fai -w 200000 > T2T-Polish.fa.new.masked_new.fa.windows.bed
awk '{OFS="\t"; print $1, $3-1, $4}' DYGY.wgdi_new.gff | sort -k1,1 -k2,2n | bedtools merge -i - > DYGY.wgdi.gene.bed
bedtools intersect -a T2T-Polish.fa.new.masked_new.fa.windows.bed -b DYGY.wgdi.gene.bed  -c > gene.circos

##准备GC分布文件
bedtools nuc -fi T2T-Polish.fa.new.masked_new.fa -bed T2T-Polish.fa.new.masked_new.fa.windows.bed  | cut -f 1-3,5 |grep -v "#"|awk '{print $1"\t"$2"\t"$3"\t"$4*100}' > gc.circos

##准备TE密度文件
#T2T-Polish.fa.mod.EDTA.TEanno.gff3：新服务器/data/xueqin/Prunus/genome_assembly/1Chromosome/06_polish/07.EDTA/T2T-Polish.fa.mod.EDTA.anno/T2T-Polish.fa.mod.EDTA.TEanno.gff3
grep -v "^scaffold_" T2T-Polish.fa.mod.EDTA.TEanno.gff3 >T2T-Polish.fa.mod.EDTA.TEanno_new.gff3
grep -v "#" T2T-Polish.fa.mod.EDTA.TEanno_new.gff3|cut -f1,4,5| sort -k 1,1 -k 2,2n |bedtools merge -i - > T2T-Polish.fa.new.masked_new.TE.bed
bedtools coverage -a T2T-Polish.fa.new.masked_new.fa.windows.bed -b T2T-Polish.fa.new.masked_new.TE.bed |awk '{print $1"\t"$2"\t"$3"\t"$7*100}' > TE.circos

##准备DNA转座子
grep "DNA" T2T-Polish.fa.mod.EDTA.TEanno_new.gff3|cut -f1,4,5| sort -k 1,1 -k 2,2n |bedtools merge -i - > T2T-Polish.fa.mod.EDTA.TEanno.DNA.bed
bedtools coverage -a T2T-Polish.fa.new.masked_new.fa.windows.bed -b T2T-Polish.fa.mod.EDTA.TEanno.DNA.bed |awk '{print $1"\t"$2"\t"$3"\t"$7*100}' > DNA.circos

###LTR数据文件
grep "LTR/Copia" T2T-Polish.fa.mod.EDTA.TEanno_new.gff3|cut -f1,4,5| sort -k 1,1 -k 2,2n |bedtools merge -i - > T2T-Polish.fa.mod.EDTA.TEanno.LTR_Copia.bed
bedtools coverage -a T2T-Polish.fa.new.masked_new.fa.windows.bed -b T2T-Polish.fa.mod.EDTA.TEanno.LTR_Copia.bed |awk '{print $1"\t"$2"\t"$3"\t"$7*100}' > LTR_Copia.circos

###LTR数据文件
grep "LTR/Gypsy" T2T-Polish.fa.mod.EDTA.TEanno_new.gff3|cut -f1,4,5 | sort -k 1,1 -k 2,2n |bedtools merge -i - > T2T-Polish.fa.mod.EDTA.TEanno.LTR_Gypsy.bed
bedtools coverage -a T2T-Polish.fa.new.masked_new.fa.windows.bed -b T2T-Polish.fa.mod.EDTA.TEanno.LTR_Gypsy.bed |awk '{print $1"\t"$2"\t"$3"\t"$7*100}' > LTR_Gypsy.circos

##准备共线性文件，见jcvi/work
conda activate jcvi
#!/bin/bash
#/data/xueqin/Project/Prunus/Prunus_Geome_analyses/Prunus_zippeliana_genome/Haphic_t2t/Circos_2025.7.10
awk -v OFS="\t" '{print $1, $3-1, $4, "DYGY"$2}' DYGY.wgdi.gff > ./jcvi/DYGY.wgdi.bed

grep -E "^Chr_(1|3|11|7|13|5|9)\b" DYGY.wgdi.bed > DYGY.SubA.bed
grep -E "^Chr_(16|2|4|12|8|14|6|15|10)\b" DYGY.wgdi.bed > DYGY.SubB.bed

grep -E "^>DYGYChr_(1|3|11|7|13|5|9)g" DYGY.wgdi.pep | sed 's/>//' > A_gene.list
grep -E "^>DYGYChr_(16|2|4|12|8|14|6|15|10)g" DYGY.wgdi.pep | sed 's/>//' > B_gene.list
seqkit grep -f A_gene.list DYGY.wgdi.pep > DYGY.SubA.pep
seqkit grep -f B_gene.list DYGY.wgdi.pep > DYGY.SubB.pep

mkdir -p test_jcvi/DYGY_SubA test_jcvi/DYGY_SubB
cp DYGY.SubA.pep test_jcvi/DYGY_SubA/DYGY_SubA.pep
cp DYGY.SubA.bed test_jcvi/DYGY_SubA/DYGY_SubA.bed
cp DYGY.SubB.pep test_jcvi/DYGY_SubB/DYGY_SubB.pep
cp DYGY.SubB.bed test_jcvi/DYGY_SubB/DYGY_SubB.bed

ln -s DYGY_SubB/DYGY_SubB.pep DYGY_SubB.pep
ln -s DYGY_SubA/DYGY_SubA.pep DYGY_SubA.pep
ln -s DYGY_SubB/DYGY_SubA.bed DYGY_SubA.bed
ln -s DYGY_SubB/DYGY_SubB.bed DYGY_SubB.bed

conda activate jcvi
python3 -m jcvi.compara.catalog ortholog --dbtype=prot --no_strip_names DYGY_SubA DYGY_SubB

#转换格式
awk 'NR==FNR{a[$4]=$1"\t"$2"\t"$3;next}NR>FNR{print a[$1]"\t"a[$2]}' ../DYGY.wgdi.bed DYGY_SubA.DYGY_SubB.lifted.anchors|awk 'NF==6' > raw_link
#添加颜色
python chuli.py raw_link tmp link.circos-1
##过滤非同源共线性：筛选 A组 ↔ B组 的连接
awk '
BEGIN {
    A["1"]; A["3"]; A["5"]; A["7"]; A["9"]; A["11"]; A["13"];
    B["2"]; B["4"]; B["6"]; B["8"]; B["10"]; B["12"]; B["14"]; B["15"]; B["16"];
}
{
    split($1, a, "_");
    split($4, b, "_");
    chr1 = a[2];
    chr2 = b[2];

    if ((chr1 in A && chr2 in B) || (chr1 in B && chr2 in A)) {
        print $0;
    }
}
' link.circos-1 > link.circos.AB
#cp link.circos.AB ../../link.circos


```

###### **圈图绘制**
```bash
#第2步，画圈图
conda activate circos
#修改circos.conf
circos -conf circos.conf
#Ai中调整circos.svg的字体大小，增加图例
#chromosomes = Chr_1;Chr_3;Chr_11;Chr_7;Chr_13;Chr_5;Chr_9;Chr_2;Chr_16;Chr_4;Chr_12;Chr_8;Chr_14;Chr_6;Chr_15;Chr_10
#chromosomes_display_default = no           #是否显示默认染色体
#调整first.circos染色体的顺序来控制最终circos图的染色体顺序
```

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2025/jpeg/54922767/1763611011095-45e76172-c1e3-458b-b6eb-d9eee11ac46e.jpeg)

###### **圈图含义解释**
<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2025/png/54922767/1752202306751-f4d22f18-ca8a-4dae-be32-9784aa7a7929.png)

###### 根据subphser划分的AB亚基因组重新绘制
```bash
#路径老服务器/data/xueqin/Project/Prunus/Prunus_Geome_analyses/Prunus_zippeliana_genome/Haphic_t2t/Circos_2025.7.10/New_Circos_subphaser_2025.11.13

cp ../DYGY.wgdi.bed ./
sed -i 's/DYGYDYGYChr/DYGYChr/g' DYGY.wgdi.bed
grep -E "^Chr_(1|2|3|5|8|12|13|15)\b" DYGY.wgdi.bed > DYGY.SubA.bed
grep -E "^Chr_(4|6|7|9|10|11|14|16)\b" DYGY.wgdi.bed > DYGY.SubB.bed

grep -E "^>DYGYChr_(1|2|3|5|8|12|13|15)g" DYGY.wgdi.pep | sed 's/>//' > A_gene.list
grep -E "^>DYGYChr_(4|6|7|9|10|11|14|16)g" DYGY.wgdi.pep | sed 's/>//' > B_gene.list

seqkit grep -f A_gene.list DYGY.wgdi.pep > DYGY.SubA.pep
seqkit grep -f B_gene.list DYGY.wgdi.pep > DYGY.SubB.pep

#获取link.circos-1后期改名为link.circos
#jcvi需要修改成DYGY_SubA.bed和DYGY_SubB.bed
mkdir SubA_SuB

python3 -m jcvi.compara.catalog ortholog --dbtype=prot --no_strip_names DYGY_SubA DYGY_SubB

#转换格式
awk 'NR==FNR{a[$4]=$1"\t"$2"\t"$3;next}NR>FNR{print a[$1]"\t"a[$2]}' ./DYGY.wgdi.bed DYGY_SubA.DYGY_SubB.lifted.anchors|awk 'NF==6' > raw_link
#添加颜色
python chuli.py raw_link tmp link.circos-1
##过滤非同源共线性：筛选 A组 ↔ B组 的连接
awk '
BEGIN {
    A["1"]; A["2"]; A["3"]; A["5"]; A["8"]; A["12"]; A["13"]; A["15"];
    B["4"]; B["6"]; B["7"]; B["9"]; B["10"]; B["11"]; B["14"]; B["16"];
}
{
    split($1, a, "_");
    split($4, b, "_");
    chr1 = a[2];
    chr2 = b[2];

    if ((chr1 in A && chr2 in B) || (chr1 in B && chr2 in A)) {
        print $0;
    }
}
' link.circos-1 > link.circos.AB
#cp link.circos.AB ../../link.circos


#需要这些信息
DNA.circos    gc.circos    link.circos       LTR_Gypsy.circos
first.circos  gene.circos  LTR_Copia.circos  TE.circos

配置circos.conf
conda activate circos
#修改circos.conf
circos -conf circos.conf

#报错but the maximum is currently set at [25000].
#两种解决方法
（1）nano /home/xueqin/miniconda3/envs/circos/etc/housekeeping.conf
#找到这行（大约在文件中间）：
max_links = 25000
修改为
max_links = 100000

（2）减少绘图连接数量
head -n 25000 link.circos > link.circos.sub
circos.conf 里改成：
<links>
file = link.circos.sub
```

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2025/png/54922767/1763015188115-3a36b72c-290b-41d4-8f31-df5f6ca12ee8.png)

