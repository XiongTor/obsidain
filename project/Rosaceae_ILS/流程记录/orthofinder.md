---
tags:
  - 流程记录
  - cytonuclear
---
- [ ] ### 说明
本流程主要用于记录orthofinder数据集进行核质冲突分析的全流程

### 处理位置：
`/data/xiongtao/project/Rosaceae/data_collect/orthofinder_v3_new/hybsuite_out_orthofinder_2`

### 数据集合
蔷薇科一共80采样，另外外类群3采样，总数83

# 1. 直系同源基因筛选
此处承接orthofinder筛选直系同源基因后的结果

利用hybsuite进行直系同源基因筛选
```
hybsuite full_pipeline \
-input_list ./namelist.txt \
-eas_dir ./hybpiper_assmble \
-output_dir ./hybsuite_out_orthofinder_2 \
-t orthofinder_v3_singel_rf.fasta \
-OI 1234567b \
-min_sample_coverage 0.1 \
-min_locus_coverage 0.1 \
-min_length 0 \
-skip_stage 01 \
-nt 12 \
-process 5
```

# 2. 系统发育树建立
```
#不再过滤物种丰富度

#过滤序列长度，建议直接查看最小的几个文件进行处理，代码执行有一定问题，速度却不一定更快
#寻找序列长度短于100bp的序列并移除
n=0
for name in ./RLWP/*.fasta;do
  n=$((n+1))
  echo $n
  tt=$(seqkit fx2tab -l -n $name|cut -f2|datamash median 1)
  if [[ $tt -le 100 ]];then
    echo $name
    echo $name>>RLWP_less100.txt
    mv $name ./less100_length/RLWP
  fi
done


#剔除序列长度短于100bp的基因序列构建的基因树
while read -r line;do
  tt=$(echo $line|cut -f3 -d'/'|cut -f1 -d'.')
  mv 07-genetrees/RT/${tt}.* 08-genetree_less100/RT
done<RT_less100.txt

#建树
for name in ./02-All_paralogs/03-Filtered_paralogs/*.fasta;do
  tt=$(basename $name .fasta)
  echo $tt
  # iqtree -s $name -m MFP -B 1000 --bnni -T 10 -pre ./03-genetree/${tt}
done1
```


# 3. 长枝去除

#### 需要特别注意的是，在treeshrink的过程中，生成的类似output_summary.txt的报表均指的是所有长枝的信息，与设定的阈值无关，因此要注意识别。最终去除的长枝，需要根据报表中的Signature值与设定的阈值进行比较，如果超过了设定的阈值，则会被去除。或者直接查看生成的最终树文件和序列文件。

有一点需要注意的是，针对**MI**的处理，要特殊一些，因为MI中存在名称极其相似的情况，例如：
```
OG0017695_2.MIortho1.selected_stripped.aln.trimmed.fasta
OG0017695_2.MIortho2.selected_stripped.aln.trimmed.fasta
```
此时仅靠“.”或者“\_”无法完全划分，需要特别注意。

```
#将每个基因的外类群序列单独提取出来

#先将每个物种的序列换行符去掉
n=0
while read -r name;do
  n=$((n+1))
  echo $n
  seqkit seq ../04.1-Alignments_leng100_s0.1/MI/${name}.*.fasta -w 0 > ./MI_seq/${name}_oneline.fasta
done<MI_genetree_rt_list.txt


m=0
for name in ./MI_seq/*.fasta;do
  m=$((m+1))
  echo $m
  tt=$(basename $name _oneline.fasta)
  grep -E -A 1 --no-group-separator "Elaeagnus_angustifolia|Zelkova_schneideriana|Morus_indica" $name> ./MI_outg_seq/${tt}_outgroup.fasta
done


#去除所有基因树中的外类群，并将对应的序列放到对应文件夹中，序列更名为input.fasta，基因树更名为input.tre
m=0
for tree in ../09-genetrees_reroot/MI/*.tre;do
  m=$((m+1))
  echo $m
  tt=$(basename $tree .selected_stripped.aln.trimmed.rt.tre)
  mkdir ./MI/$tt
  pxrmt -t $tree -n Elaeagnus_angustifolia,Zelkova_schneideriana,Morus_indica>./MI/$tt/input.tre
  pxrms -s ./MI_seq/${tt}*.fasta -n Elaeagnus_angustifolia,Zelkova_schneideriana,Morus_indica>./MI/$tt/input.fasta
done

#运行treeshrink,注意路径
nohup run_treeshrink.py -i ./ -t input.tre -a input.fasta -b 20 -q 0.2 > ./input.tree.treeshrinklog.txt &

#查看全部的长枝报表，本步骤中阈值参数设置无用，最终输出的报表一致，为所有长枝的相关信息
run_treeshrink.py  -t MO_orthofinder_genetrees.tre -q 0.01 -b 1 -o treeshrink_multi_MO -O shrunk_0.01

#更改输出文件名称,增加外类群序列
a=0
for name in `ls -d */`;do
  a=$((a+1))
  echo $a
  tt=$(basename $name /)
  mv $tt/output1.tre $tt/${tt}_trimmed_rt_noout_output.tre
  cat ../MI_outg_seq/${tt}*.fasta>>$tt/output1.fasta
  mv $tt/output1.fasta $tt/${tt}_trimmed_haveoutg_output.fasta
done


#建树
ls -d */|sed 's/\///g'>namelist.txt


while read -r line;do
  cp ${line}/*output.fasta seq_data 
done<namelist.txt

### 值得注意的是，treeshrink似乎会在去除序列后自动做序列修剪，导致按照此流程，插入的原序列的外类群会和treeshrink后的序列无法对齐，从而导致建树错误，因此建议在此时重新比对修剪


for name in `ls seq_data`;do
  echo $(basename $name .fasta).mafft
  mafft --auto seq_data/$name > mafft/$(basename $name .fasta).mafft
done

for name in ls mafft/*.mafft; do
  echo ${name}.tri.fasta
  output_file="trimal/$(basename "$name" .mafft).tri.fasta"
  trimal -in "$name" -out "$output_file" -automated1
done

for name in trimal/*.fasta;do
  tt=$(basename $name .fasta)
  iqtree -s $name -m MFP -B 1000 --bnni -T 10 -pre ./genetrees/${tt}
done

-----------------
while read -r line;do
  iqtree -s $line/${line}_trimmer_haveout_output.fasta -m MFP -B 1000 --bnni -T 10 -pre ./genetree_b20/${line}
done<namelist.txt
```

## treeshrink后建树并置根

```
#建树脚本 mafft_tri_iqtre.sh
mkdir mafft trimal genetrees
for name in `ls seq_data`;do
  echo $(basename $name .fasta).mafft
  mafft --auto seq_data/$name > mafft/$(basename $name .fasta).mafft
done

for name in ls mafft/*.mafft; do
  echo ${name}.tri.fasta
  output_file="trimal/$(basename "$name" .mafft).tri.fasta"
  trimal -in "$name" -out "$output_file" -automated1
done

for name in trimal/*.fasta;do
  tt=$(basename $name .fasta)
  iqtree -s $name -m MFP -B 1000 --bnni -T 15 -pre ./genetrees/${tt}
done


#置根
mkdir reroot_genetrees
cd reroot_genetrees

for tree in ../genetrees/*.treefile; do
    nn=$(nw_labels -I $tree|grep -c -E "Elaeagnus_angustifolia|Zelkova_schneideriana|Morus_indica")
    if [[ $nn -gt 0 ]]; then
        tt=$(nw_labels -I $tree|grep -E "Elaeagnus_angustifolia|Zelkova_schneideriana|Morus_indica"|paste -sd,)
        mm=$(basename $tree .treefile)
        pxrr -t $tree -g $tt > ./${mm}.rt.tre
    else
        Rscript /home/xiongtao/data/scripts/mad_reroot.R $tree
    fi
done

#建立物种树
	cat ../reroot_genetrees/*.tre >rosa_LS_genetrees.tre
	
	# ASTRAL_pro3
~/data/software/ASTER/bin/astral-pro3 -i rosa_LS_genetrees.tre -o rosa_LS_sp.tre>log_astral-pro3.txt

#置根
pxrr -t rosa_LS_sp.tre -g Elaeagnus_angustifolia,Zelkova_schneideriana,Morus_indica > rosa_LS_sp_treeshrink_rt.tre

#单系性检测
pxrlt -t rosa_MO_sp_treeshrink_rt.tre -c old_name.txt -n new_name.txt > rosa_MO_sp_treeshrink_rt_rn.tre
```

## treeshrink前后比较

### 比较支持率
```
Rscript ~/data/scripts/Get_support_value.R MO_orthofinder_sp_rt.tre MO_supp.csv
Rscript ~/data/scripts/Get_support_value.R rosa_MO_sp_treeshrink_rt.tre MO_treeshrink_supp.csv

# 计算平均值,中值，最大值，最小值
cat MO_treeshrink_supp.csv|cut -f2 -d','|sed 1d |datamash mean 1 median 1 max 1 min 1

```
#### LS
**treeshrink前**
   平均值                      中值          最大值        最小值
0.8680675125     0.9993155         1          0.433015

**treeshrink后**
0.8473398625    0.999259        1       0.373488

#### MI
**treeshrink前**
0.8100125875    0.963878        1       0.36617

**treeshrink后**
0.8096063625    0.944425        1       0.358091

#### MO
**treeshrink前**
0.8320790375    0.9964475       1       0.350721

**treeshrink后**
0.8218946375    0.9922275       1       0.386709

### 比较拓扑结构差异
```
#cophylo

#RF path distance
Rscript ~/data/scripts/count_RF_path_two_trees.R MO_orthofinder_sp_rt.tre rosa_MO_sp_treeshrink_rt.tre
```

#### LS
RF_Distance                        Path_Distance
0.164556962025316       72.6154253585283

#### MI
RF_Distance                       Path_Distance
0.126582278481013      72.4154679609267

#### MO
RF_Distance                       Path_Distance
0.177215189873418      69.4838110641608

## treeshrink前后的数据量差异
### LS 
59236/60673

#### MI
175810/185351

#### MO
183644/192116

### treeshrink后与ags353物种树的差异
### LS 
RF_Distance,Path_Distance
0.316455696202532,143.930538802577
#### MI
RF_Distance,Path_Distance
0.392405063291139,139.785550040052
#### MO
RF_Distance,Path_Distance
0.367088607594937,131.912850018488

## 最终决定
orthofinder的数据集合还是选择MO数据集，其实针对三个数据集而言
- 它们建立得到的系统发育树均在族水平单系性良好
- 它们的支持率相差不多
 而对于MO的数据集而言，它的主要优势在于数据量保存的较多。
 LS的数据集其中无外类群的基因太多了，导致最终有一半的数据集都被去除。最终可用的为1278/2986，包含1278个基因。treeshrink后，剩余的序列数量为59236/60673
 
 MI的数据集也是类似的，一共17000多条序列，有5000条左右都没有外类群。最终可用的为12416/17109，包含2933个基因。treeshrink后，剩余的序列数量为175810/185351
 
 而MO的数据相对来说就更加完整一点，其中大部分数据都具有外类群，无外类群的数量<1000条，最终可用的为12568/13097,包含2938个基因。treeshrink后，剩余的序列数量为：183644/192116

而且从与AGS353树的差异来看，也是相差无几

所以最终选择**MO数据集作为后续分析的数据集**。且需要进行treeshrink，阈值就确定为0.2，因为后续ILS的判断就是依靠长枝，因此我们只想要去掉最突出的长枝。


## MO的最终数据集相关数据
物种数量：
基因数量：
序列数量（trimal前，treeshrink前后）：183644/192116

### 小插曲，将所有的Osteomeles_schwerinae替换为Osteomeles_schweriniae
```
#针对序列
m=0
for seq in old_seq/*.fasta;do
  m=$((m+1))
  echo $m
  tt=$(basename $seq)
  pxrls -s $seq -c ../old_name.txt -n ../new_name.txt>./${tt}
done
#针对树文件

m=0
for tree in old_tree/*.tre;do
  m=$((m+1))
  echo $m
  tt=$(basename $tree)
  pxrlt -t $tree -c ../old_name.txt -n ../new_name.txt>${tt}
done
```

### 移除treeshrink后的trimal中序列长度低于100的序列，调整全为gap的序列，并建树检测单系性
```
#判断并移除对应序列
n=0
for name in ./05-trimal/*.fasta;do
  n=$((n+1))
  echo $n
  tt=$(seqkit fx2tab -l -n $name|cut -f2|datamash median 1)
  if [[ $tt -le 100 ]];then
    echo $name
    echo $name>>trimal_less100.txt
    mv $name ./remove_less100_trimal
  fi
done

#移除对应基因树
while read -r line;do
  mv 07-reroot-genetrees/${line}* remove_less100_reroot_genetrees
done<trimal_less100.txt

#去除全是gap的序列
while read -r name;do
  echo $name
  mv 05-trimal/${name}* 05-trimal_mantgap
  seqkit grep -s -r -p "[ACGTacgt]" 05-trimal_mantgap/${name}*> 05-trimal/${name}_trimmed_haveoutg_output.tri.fasta
done<more_trimal.txt

#对这部分序列重新建树
while read -r name;do
  iqtree -s 05-trimal/${name}* -m MFP -B 1000 --bnni -T 10 -pre ./06-genetrees/${name}
done<more_trimal.txt

#基因树置根
m=0
for tree in ../06-genetrees/*.treefile; do
    m=$((m+1))
    echo $m
    nn=$(nw_labels -I $tree|grep -c -E "Elaeagnus_angustifolia|Zelkova_schneideriana|Morus_indica")
    if [[ $nn -gt 0 ]]; then
        tt=$(nw_labels -I $tree|grep -E "Elaeagnus_angustifolia|Zelkova_schneideriana|Morus_indica"|paste -sd,)
        mm=$(basename $tree .treefile)
        pxrr -t $tree -g $tt > ./${mm}.rt.tre
    else
        Rscript /home/xiongtao/data/scripts/mad_reroot.R $tree
    fi
done

#建物种树
astral-pro3 -i rosa_orthofinder_genetrees.tre -o rosa_orthofinder_sptree.tre

#置根
pxrr -t rosa_orthofinder_sptree.tre -g Zelkova_schneideriana > rosa_orthofinder_sptree_rt2.tre

#单系性检测
pxrlt -t rosa_orthofinder_sptree_rt.tre -c old_name.txt -n new_name.txt > rosa_orthofinder_sptree_rt_rn.tre

```

## 系统发育树折叠
使用nw_ed折叠bs值低于10%和20%的基因树分支，然后在此推断物种树，并与之前的物种树结果进行比较，观察差异，判断是否需要重新进行一些分析
```
for tree in ./*.tre;do
  tt=$(basename $tree .tre)
  nw_ed $tree 'i & b < 10' o > ${tt}_collapse_10.tre
done

for tree in ./*.tre;do
  tt=$(basename $tree .tre)
  nw_ed $tree 'i & b < 20' o > ${tt}_collapse_20.tre
done


#建立物种树
	cat ../collapse_20/*.tre >rosa_orthofinder_genetrees_collapse20.tre
	
	# ASTRAL_pro3
astral-pro3 -i rosa_orthofinder_genetrees_collapse20.tre -o rosa_orthofinder_sptree_collapse20.tre

#置根
pxrr -t rosa_orthofinder_sptree_collapse20.tre -g Elaeagnus_angustifolia,Zelkova_schneideriana,Morus_indica > rosa_orthofinder_sptree_rt_collapse20.tre

#单系性检测
pxrlt -t rosa_orthofinder_sptree_rt_collapse20.tre -c ../old_name.txt -n ../new_name.txt > rosa_orthofinder_sptree_rt_rn_collapse20.tre

#比较物种树指标以及拓扑结构
Rscript ~/data/scripts/Get_support_value.R rosa_orthofinder_sptree_rt_collapse20.tre supp.csv

cat supp.csv|cut -f2 -d','|sed 1d |datamash mean 1 median 1 max 1 min 1

# 从上到下依次为无处理，<10和<20的支持率
0.8216090125    0.9913835       1       0.375404
0.8137628125    0.9855815       1       0.37082
0.8088674125    0.9777195       1       0.372977

```


# 4.节点信息计算
## 计算theta值
```
iqtree3 -s rosa_supermatrix.fasta -g rosa_orthofinder_MO_treeshrink_sp_rt.tre -p partition.txt -m MFP -B 1000 --bnni -T 20 -pre rosa_orthofinder_iqtree
```


# 12-MSCquartet
考虑到本研究包含有三个外类群 ，且部分基因树没有外类群，靠MAD置根。因此计划使用自定义脚本为无外类群的基因树添加外类群。
同时将外类群倒塌为一个‘outgroup’
```
### add_outgroup_gene_specific_change.py
python add_outgroup_gene_specific_multi.py \

        --input_trees 04-genetree_reroot/ \

        --outgroups Zelkova_schneideriana Morus_indica Elaeagnus_angustifolia \

        --target_outgroup Zelkova_schneideriana \

        --output_dir 04-1-python_add_out_group_genetrees/ \

        --sensitivity 0.2 \

        --histogram
```
# Dsuite
```
#合并超矩阵
pxcat -s ../08-trimal_with_outgroup/*.fasta -p rosa_sp_partition.txt -o rosa_sp_supermatrix.fasta
```

# QuIBL

### 方法流程

#### 1. 安装
要求python2.7的环境，所以先创建环境
```
conda create -n python2.7 python=2.7
conda activate python2.7
```
同时需要安装特定版本的依赖包
```
conda install ete3==3.0.0b35  
conda install joblib==0.11
```
通过运行示例文件来判断是否配置成功
```
python QuIBL.py ./Small_Test_Example/sampleInputFile.txt
```

#### 2. 输入文件
QulBL运行较为简单，只需要准备两个文件
- 置根后的基因树合并文件，smallTestTrees.txt
- 配置文件，sampleInputFile.txt

配置文件示例如下：
```
[Input]
treefile: /genetrees.nwk
numdistributions: 2
likelihoodthresh: 0.01
numsteps: 30
gradascentscalar: 0.5
totaloutgroup: outgroup_tip
multiproc: True
maxcores:48

[Output]
OutputPath: ./res.csv
```

相关参数解释如下，一般使用默认参数就好，需要变动的主要参数为`treefile` 和`totaloutgroup`
```
treefile: The path to the trees to be analyzed.  
  
numdistributions: The number of branch length distributions in the mixture to test. For now, only two is supported (this corresponds to one ILS and one non-ILS distribution).  
  
likelihoodthresh: The maximum change in likelihood allowed for the gradient ascent search for theta to stop.  
  
numsteps: The number of total EM steps. For thousands of trees, we reccomend trying around 50.  
  
gradascentscalar: The factor to shrink the stepsize when a gradient ascent step fails.  
  
totaloutgroup: The name of the ultimate outrgroup of your sample. All trees are assumed to be rooted using this taxon.  
  
multiproc: Accepts `True` or `False` and either turns multiprocessing on or off.  
  
OutputPath: Where the output gets written.  
  
maxcores: The maximum number of cores QuIBL is allowed to use.
```

**基因树文件**，建议基因树的tips label不要带下划线“\_“，因为最终结果中会以下划线来分隔每个三分的物种，容易造成混淆。同时**值得注意的是基因树文件配置有两条思路，分别对应不同的情况**

#### 1）基因树文件中**不存在样本缺失**，所有的基因树都包含所有的取样，此时只需要直接运行如下代码即可：
```
python /QuIBL-master/QuIBL.py sampleInputFile.txt
```

#### 2）基因树文件中**存在样本缺失**，此时流程较为复杂，需要通过提取子树来避免有样本缺失的问题，流程如下：
  涉及到如下几个主要步骤：
  - 01_comb_4spes.py，通过物种树来提取所有可能的三物种组合
  - 02_extract_subtree_for_4spes.py，抽取所有的四物种组合树，多出的一个物种为外类群
  - 去除祖先节点序号，生成配置文件sampleInputFile.txt
  - 批量运行QulBL并合并全部结果


在处理之前，值得注意的是，由于本课题中使用了三个外类群，并且他们并不是都存在于全部的基因树中，因此这里我们进行了一个简单的处理，即每个基因树保留一个外类群进行后续的分析，保留的顺序按照353筛选的个数来，为Zelkova.schneideriana > Elaeagnus.angustifolia > Morus.indica。并在之后统一更改名称为Outgroup。
此外，在此过程中，我们发现部分基因树未能成功置根，主要是由于外类群并非单系导致的。但我们认为这种情况很可能反映了某种真实的生物学过程，例如ILS，渐渗杂交等。因此我们还是决定保留这部分基因。同时由于置根并不会导致内类群的枝长发生大幅改变，因此对于QuIBL分析几乎没有影响，因此决定不对这部分数据进行处理。
其中部分代码如下：
```
for name in 07-1-old_tree_python_QuIBL/*.tre;do
  tt=$(basename $name);   
  z=$(nw_labels -I $name|grep -c "Zelkova.schneideriana");   
  e=$(nw_labels -I $name|grep -c "Elaeagnus.angustifolia");   
  m=$(nw_labels -I $name|grep -c "Morus.indica");   
  if [[ $z -eq 1 ]];then     
    rm=$(nw_labels -I "$name" | grep -E "Elaeagnus.angustifolia|Morus.indica" | paste -sd "," -);     
    echo $name "z";     
    pxrmt -t $name -n $rm>./QuIBL_genetree_rt/$tt;     
    pxrlt -t ./QuIBL_genetree_rt/$tt -c QuIBL/old_z.txt -n QuIBL/new.txt >./QuIBL_genetree_rt_final/$tt;   
  elif [[ $e -eq 1 ]];then     
    pxrmt -t $name -n Morus.indica>./QuIBL_genetree_rt/$tt;     
    echo $name "e";     
    pxrlt -t ./QuIBL_genetree_rt/$tt -c QuIBL/old_e.txt -n QuIBL/new.txt >./QuIBL_genetree_rt_final/$tt;   
  else     
    echo $name "m";     
    pxrlt -t $name -c QuIBL/old_m.txt -n QuIBL/new.txt >./QuIBL_genetree_rt_final/$tt;   
  fi; 
 done
 
 #此循环并未完全囊括全部情况，注意检查最终外类群的数量以及是否替换为了Outgroup
```


  具体代码流程如下：
**01_comb_4spes.py**
```
  # coding=utf-8
from itertools import combinations
from ete3 import Tree
# 给定的元素
elements = ['aawi', 'acer', 'acyt', 'adig', 'aflo', 'ahya', 'aint', 'amic', 'amil', 'amur', 'anas', 'apal', 'asel', 'aten', 'ayon']

# 提取三个元素为一组
combs_3 = list(combinations(elements, 3))

# 每组加上'outgroup'形成四个元素
combs_4 = [(comb[0], comb[1], comb[2], 'Outgroup') for comb in combs_3]

# 将每个组合写入文件中，每行一个组合
with open('out_four_species_array.txt', 'w') as file:
        for comb in combs_4:
                file.write(' '.join(comb) + '\n')
```

**02_extract_subtree_for_4spes.py**,对此脚本进行了一定程度的修改，使得可以多线程运行，因此要求python3.0以上环境
对于orthofinder进行直系同源基因筛选后的MO数据集，有一个问题，即paragone的算法会将原本的一棵基因树提取打断为多棵，从而导致有一些三联体组合，可能在所有打断的基因树中都不会存在，导致这一步最终提取出来的三联体树的数量少于预期，目前根据结果，提取出的总数为77588/79079=98.11%
```
# coding=utf-8
from ete3 import Tree
from multiprocessing import Pool
import os

file_path = '../01-comb_4spes/out_four_species_array.txt'
tree_path = '../rosa_ags353_genetrees.tre'
output_dir = './'
os.makedirs(output_dir, exist_ok=True)

# ==== 先把所有树读入内存 ====
with open(tree_path, 'r') as tf:
    all_trees = [line.strip() for line in tf if line.strip()]

# ==== 定义单个组合处理函数 ====
def process_line(args):
    line, b = args
    subtree_taxa = line.strip().split(' ')
    result_array = []

    for treeline in all_trees:
        t = Tree(treeline)
        leaf_names = set(t.get_leaf_names())

        if not all(tip in leaf_names for tip in subtree_taxa):
            continue

        t.prune(subtree_taxa, preserve_branch_length=True)
        result_array.append(t.write())

    if result_array:
        filename = os.path.join(output_dir, f"out_subtree_{b}.txt")
        with open(filename, 'w') as outfile:
            for element in result_array:
                outfile.write(element + '\n')

# ==== 主程序 ====
if __name__ == '__main__':
    with open(file_path, 'r') as file:
        lines = [line for line in file if line.strip()]

    b = 1
    batch_size = 20

    for i in range(0, len(lines), batch_size):
        batch = lines[i:i + batch_size]
        args_list = [(line, b + j) for j, line in enumerate(batch)]

        # 并行执行
        with Pool(processes=batch_size) as pool:
            pool.map(process_line, args_list)

        b += len(batch)


```

产生多个4物种组合的基因树文件，对其进行处理，将祖先节点的序号去掉
```
n=1
for file in out_subtree_* ;do 
  n=$((n+1))
  echo $n
  sed -i 's/)\([0-9]*\):/):/g' $file
done
```

批量产生sampleInputFile.txt文件，注意修改路径和outgroup：
```
#提取外类群信息并生成对应的运行文件
n=1
for file in out_subtree_* ;do 
n=$((n+1))
echo $n
echo -e "[Input]\n\
treefile: ../02-four-sp-array/$file\n\
numdistributions: 2\n\
likelihoodthresh: 0.01\n\
numsteps: 50\n\
gradascentscalar: 0.5\n\
totaloutgroup: Outgroup\n\
multiproc: True\n\
maxcores:70\n\
\n\
[Output]\n\
OutputPath: ./out${file}.csv\n" > ./run_${file}
done
```

**03_run.sh**，批量运行脚本，要求python2.7
```
#构建批量运行的sh文件03_run.sh  
for file in run_out_subtree*;do 
  echo -e "python ~/data/software/QuIBL-master/QuIBL.py ../02-four-sp-array/$file";
done > ../03-run/03_run.sh
  
#利用Parafly并行跑程序，该软件在python2和python3环境下均可以利用conda安装,注意调整CPU占用量
nohup ParaFly -c ./03_run.sh -CPU 60 &

#同时检查报错汇总文件`FailedCommands`并运行，具体说明见“3.常见报错”
cp FailedCommands run_failcommands.sh
sed -i 's/QuIBL.py/cython_vers\/QuIBL_cyth.py/g' run_failcommands.sh
bash run_failcommands.sh
```

最终结果整合：
```
for file in ../03-run/*.csv; do 
  sed -n '2,$p' "$file" >> results_qulbl.txt; 
done

sed -i '1i triplet,outgroup,C1,C2,mixprop1,mixprop2,lambda2Dist,lambda1Dist,BIC2Dist,BIC1Dist,count' results_qulbl.txt
```

#### 3. 常见报错
```
ValueError: math domain error
```

这个报错是由于`L+=log(temp)`中temp值的异常导致的数学运算错误。往上追溯会发现temp的异常是`cArray=[nan, nan], lmbd=nan`中的NAN导致的无法识别。NAN的异常又是由于脚本中在进行EM拟合时步长调节过度导致的，即在规定的步数内，步长太长跳过了最优值导致最终的λ发散或为负数而显示的异常。虽然代码中已有相关调节措施避免此类情况，但显然还是存在一定的bug。
> EM 拟合的目标就是用混合分布模型，最大限度解释输入的分支长度数据，并估计这些数据是由 ILS 和 introgression 以何种比例产生的。

因此我们目前有两种思路去规避这个报错：
1)  调参，调节参数避免出现这种问题
2)  编译cython后使用作者的另一个脚本运行

##### 1) 调参
- 减小`gradascentscalar`，这是控制 λ（`lmbd`）变化速度的因子，减小它能有效避免步长太大导致 λ 趋近 0 或负数，从而触发 `NaN`
- 提高 `likelihoodthresh`，减少陷入“震荡”区域的迭代次数
- 增加 `numsteps`，增加调节次数去避免`NAN` 
值得注意的是，调节参数本身具有一定效果但不一定能完全适配所有数据。同时调参后会导致运行时间延长，也可能造成其它报错

##### 2) cython编译
作者提供了cython编译的另一个类似脚本可以解决上述报错，流程如下：

在创建的python2.7的环境下安装cython
```
conda install cython
```

编译cython，在下载的QulBL的安装文件中有帮助实现编译的文件，位置在`QulBL/cython_vers/setup.py`，同样的新的QulBL脚本也在该目录下，编译代码如下：
```
python setup.py build_ext --inplace
```

编译完成后使用新的脚本`QuIBL_cyth.py`运行那些不能正常跑出结果的文件即可，示例如下：
```
python QuIBL_cyth.py run.txt
```
#### 4. 结果展示与画图

让我们来简单分析一下最终结果，即你汇总的`results_qulbl.txt`文件中的结果。以下是GitHub中的一个例子，其物种树的拓扑为：(((A,B),C),D);
![](../../../imag/QulBL/file-20250608155456743.png)
```
triplet：所分析的三联体结构
outgroup：以哪个tip为外群
C1：仅ILS模型下分支的内部枝长，默认为0
C2：双分布模型下的内部枝长，如果拓扑与物种树一致，反映两次物种分化事件之间的时间间隔。如果不一致，反映的是渗入事件与所有三个分支共祖之间的时间间隔
mixprop1：ILS发生的概率比例
mixprop2：渗入事件发生的概率比例
lambda1Dist：仅ILS模型下的拓扑枝长缩放比例，可以理解为标准化枝长使得它可以用于检测ILS和渐渗
lambda2Dist：同上，但是双模型
BIC1Dist：仅ILS模型下的贝叶斯得分,通常 BIC 差值 >10 被认为显著支持
BIC2Dist：混合模型下的贝叶斯得分，越小越好
count：此拓扑在输入的所有拓扑中出现的次数
```
>部分解释可能存疑，如C1/2和lambda的解释

根据如上解释，我们可以发现，当C为外类群时，拓扑与物种树一致，此时判断基因树物种树一致，不参与总体的ILS/渐渗的测评中。即我们认为对于所有位点而言，有0.9\*1000/(1000+100+10000)=0.89%的位点是由于渐渗导致的物种树与基因树不一致，而只考虑与物种树不一致的位点的话，这个概率来到了81%


##### 结论解释中的部分难点

1）如何计算两个物种间的渐渗概率，基于此需了解以下几点
- 两个物种广泛存在于不同的三联体中，如何计算这两个物种间的渐渗概率，教程中的两种算法最终得出的结果有哪些区别
- 是否考虑概率计算是基于哪个模型（ILS混合还是仅ILS）做出的
- 是否考虑显著性 

2）如何得出不同三联体的内部枝长分布频率
- 数值如何计算 √
- 如何根据数据直方图拟合出两种模型的最适曲线  √

3）ILS的概率如何计算
- 是否可以基于渐渗的算法直接替换


## Simulate
主要用于模拟不同程度的ILS，并与实际的数据集进行比较，判断基因树的不一致是否可以完全用ILS来解释。  

主要参考文献：[@Jianxiang Ma, Pengchuan Sun, Dandan Wang, Zhenyue Wang, Jiao Yang, Ying Li, Wenjie Mu, Renping Xu, Ying Wu, Congcong Dong, Nawal Shrestha, Jianquan Liu, Yongzhi Yang\_2021](../../../references/@Jianxiang%20Ma,%20Pengchuan%20Sun,%20Dandan%20Wang,%20Zhenyue%20Wang,%20Jiao%20Yang,%20Ying%20Li,%20Wenjie%20Mu,%20Renping%20Xu,%20Ying%20Wu,%20Congcong%20Dong,%20Nawal%20Shrestha,%20Jianquan%20Liu,%20Yongzhi%20Yang_2021.md)

IQTREE重新估算枝长，用于计算theta值：
```
    raxmlHPC -f e -t rosa_Astral_species_sortadata.tre -m GTRGAMMA -s ../rosa_sp_supermatrix_sortadata.fasta -n rosa_astral_species_br.tre
    iqtree -s rosa_orthofinder_sp_supermatrix.fasta -p rosa_orthofinder_partition.txt -g rosa_orthofinder_sp_rt.tre -m MFP -B 1000 --bnni -T 20 -pre ./rosa_orthofinder
```
借鉴该文献中GitHub所用脚本并简单修改如下:
phybase_simulate.R
```
#!/usr/bin/env Rscript
# Author: Tao Xiong
# Date: 2025-10-24
# Description: To simlulate different

# ==== Main Script Start ====
library(phybase)

#注意将mptree1替换为自己的参考树，一般为物种树
# mptree1 = "(Gbi:8.4,(Atr:7.4,(Efe:7.2,((Peq:4.0,(Mac:3.4,Osa:3.4):0.55):2.1,((Lch:4.6,(Pam:1,Cka:1):3.6):1.3,(Cde:5.9,((Vvi:3.1,(Ath:2.7,Ppe:2.7):0.40):1.7,Aco:4.8):1.0):0.09):0.08):1.07):0.21):1);"


spname <- species.name(mptree1)
nodematrix <- read.tree.nodes(str=mptree1, name=spname)$nodes

theta_levels <- c(0.5, 1, 2, 5)   # 不同程度的 ILS（从弱到强）
n_gene <- 2000                    # 每组生成2000棵基因树

for (theta in theta_levels) {
  nodematrix[,5] <- theta
  
  genetrees <- character(n_gene)
  for (i in 1:n_gene) {
    genetrees[i] <- sim.coaltree.sp(rootnode=max(nodematrix[,1]),
                                    nodematrix=nodematrix,
                                    nspecies=length(spname),
                                    seq=rep(1, length(spname)),
                                    name=spname)$gt
  }
  
  outname <- paste0("sim_theta_", theta, ".tre")
  write(genetrees, outname)
  cat("Done: theta =", theta, "\n")
}
```

计算实际数据集的theta值

## ILS_IH_GTEE
由于后续流程均需要基因树置根，因此本次流程针对外类群进行了优化，即只保留一个外类群
```
for name in ./*_nosupp.tre;do
  tt=$(basename $name .tre);   
  z=$(nw_labels -I $name|sort|uniq|grep -c "Zelkova_schneideriana");   
  e=$(nw_labels -I $name|sort|uniq|grep -c "Elaeagnus_angustifolia");   
  m=$(nw_labels -I $name|sort|uniq|grep -c "Morus_indica");   
  if [[ $z -eq 1 ]];then     
    rm=$(nw_labels -I "$name" | grep -E "Elaeagnus_angustifolia|Morus_indica" | paste -sd "," -);     
    echo $name "z";
    if [[ -n $rm ]];then
      pxrmt -t $name -n $rm>./${tt}_oneoutg.tre;     
      pxrlt -t ./${tt}_oneoutg.tre -c ../old_z.txt -n ../new.txt >./${tt}_oneoutg_final.tre;  
    else
      pxrlt -t ./${name} -c ../old_z.txt -n ../new.txt >./${tt}_oneoutg_final.tre
    fi   
  elif [[ $e -eq 1 ]];then     
    pxrmt -t $name -n Morus_indica>./${tt}_oneoutg.tre;     
    echo $name "e";     
    pxrlt -t ./${tt}_oneoutg.tre -c ../old_e.txt -n ../new.txt >./${tt}_oneoutg_final.tre;   
  else     
    echo $name "m";     
    pxrlt -t $name -c ../old_m.txt -n ../new.txt >./${tt}_oneoutg_final.tre;   
  fi; 
 done
 ```
### 1. GTEE，模拟基因树
需要准备：
- 枝长不为零的物种树（基于ASTRAL推断）
- 基因树的集合
- 基因树的bootstrap树的集合（使用IQTREE参数-B获得的1000次近似快速采样，也可以用于此次模拟，可以将采样缩减至100次）

随机采样并合并bootstrap树的集合

#若IQTEE中采用的是-B，采用了近似随机1000次采样的bootstrap值计算，则随机采样100棵树并合并bootstrap树的集合
for file in ../03-genetrees/*.ufboot; do
  tt=$(basename $file .ufboot)
  shuf -n 100 "$file" > ./bootstrap_100/${tt}_100.ufboot
done

#对每个基因的100个bootstrap树集合进行置根
for tree in ./*.genetrees; do
    tt=$(nw_labels -I $tree|sort|uniq|grep -E "Elaeagnus_angustifolia|Zelkova_schneideriana|Morus_indica"|paste -sd,)
    mm=$(basename $tree .genetrees)
    pxrr -t $tree -g $tt > ./${mm}_rt.tre
done

#将每一个bootstrap的树合并，一共100个
# 每个文件有 100 行
for i in $(seq 1 100); do
    out="bootstrap_${i}.tre"
    echo "生成 $out"
    for f in ../bootstrap_100/*_oneoutg_final.tre; do
        sed -n "${i}p" "$f"
    done > "$out"
done

#批量合并物种树
for i in $(seq 1 100); do
  echo "======== start bootstral_${i} ========"
  astral-pro3 -i bootstrap_${i}.tre -o rosa_ags353_bootstrap_${i}_sp.tre
  pxrr -t rosa_ags353_bootstrap_${i}_sp.tre -g Outgroup > rosa_ags353_bootstrap_${i}_sp_rt.tre
  Rscript ../script/clean_supp.R rosa_ags353_bootstrap_${i}_sp_rt.tre rosa_ags353_bootstrap_${i}_sp_rt_nosupp.tre
done

#此时可以先对外类群进行处理，只保留一个外类群，同时将名称替换为Outgroup
#合并bootstrap物种树
cat bootstrap_trees/rosa_ags353_bootstrap_*_sp_rt_nosupp.tre>rosa_ags353_bootstrap_sptrees_rt_nosupp.tre

#对原本的物种树进行处理，只保留一个外类群，去除支持率，将0枝长增加一点
pxrmt -t rosa_orthofinder_sp_rt.tre -n Elaeagnus_angustifolia,Morus_indica>rosa_orthofinder_sp_rt_oneoutg.tre

sed -i 's/Zelkova_schneideriana/Outgroup/g' rosa_orthofinder_sp_rt_oneoutg.tre

Rscript script/make_treelength_gt_0.R rosa_orthofinder_sp_rt_oneoutg.tre

rm rosa_orthofinder_sp_rt_oneoutg.tre

mv tree_without_zero_length.tre rosa_orthofinder_sp_rt_nozero_oneoutg.tre
```

开始模拟流程
```
#模拟树
#由于流程代码设定有geneTr_sim，因此先创建一个一样的文件夹

mkdir geneTr_sim

Rscript --vanilla ./script/MSC_geneTr_simulator.R rosa_orthofinder_sp_rt_nozero_oneoutg.tre rosa_orthofinder_bootstrap_sptrees_rt_nosupp.tre rosa_orthofinder_genetrees_new.tre

rm geneTr_sim/*.tem.genetrees

#excepted result: sets of simulated gene trees in the folder geneTr_sim

#对模拟树置根
for n in $(seq 1 100);do
  echo "======== BP${n}.sim.genetrees ========"
  for i in $(seq 1 317); do
    sed -n "${i}p" BP${n}.sim.genetrees>test.tre
    pxrr -t test.tre -g Outgroup > test_rt.tre
    cat test_rt.tre>>BP${n}.sim_rt.genetrees
  done
done


# 为经验数据集合模拟数据集计算triplet frequency
#物种数量超过30则会及其耗时，建议使用并行处理
# 经验树
python script/triple_frequency_counter.py rosa_ags353_genetrees.tre rosa_ags353_treeshrink_sp_rt_nosupp_nozero_oneoutg_final.tre

mv rosa_ags353_genetrees.trp.tsv rosa_ags353_treeshrink_sp_rt_nosupp_nozero_oneoutg_final.trp.tsv

#excepted result: *.trp.tsv
#format: column1--species names of the triplet, sorted alphabetically; column2--triplet frequencies of (sp1,sp2);column3--triplet frequencies of (sp1,sp3);column4--triplet frequencies of (sp2,sp3).

  

# 模拟树
# !!!! Note, do not use the "./BP.sim.genetrees" as the input, remove the "./", just use "BP.sim.genetrees" is ok. !!!!

ls *.sim_rt.genetrees | parallel -j 30 'python ../script/triple_frequency_counter.py {} ../rosa_ags353_treeshrink_sp_rt_nosupp_nozero_oneoutg_final.tre'


# Find significantly unbalanced triplets and map to species tree
# !!! The GitHub orgin script is wrong, the input file should be the genetrees,not the species tree. You can check the `find_unbalanced_triplets.py`

python /data/xiongtao/tree/ILS/gene_flow/gene_flow_analy/script/find_unbalanced_triplets.py $geneTr


#result in unbalanced.trp.tsv

python /data/xiongtao/tree/ILS/gene_flow/gene_flow_analy/script/triplet_mapper.py $speciesTr unbalanced.trp.tsv

#result in unbalanced_triples_raw_count.tre and unbalanced_triples_perc_reticulation_index.tre

---

## 相关文献链接

- [[@Jianxiang Ma, Pengchuan Sun, Dandan Wang, Zhenyue Wang, Jiao Yang, Ying Li, Wenjie Mu, Renping Xu, Ying Wu, Congcong Dong, Nawal Shrestha, Jianquan Liu, Yongzhi Yang_2021]]
