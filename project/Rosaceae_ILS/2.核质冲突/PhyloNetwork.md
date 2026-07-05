---
title: "PhyloNetwork"
date: "2026-02-10"
authors: Tao Xiong
tags:
---
### 参考文献
[@PhyloNetworks_ A Package for Phylogenetic Networks_2017](references/@PhyloNetworks_%20A%20Package%20for%20Phylogenetic%20Networks_2017.md)
### Github
[GitHub PhyloNetworks](https://github.com/JuliaPhylo/PhyloNetworks.jl)
### 其它教程
[教程一](https://wu-tz.github.io/2023/10/07/phylonetwork/) 2024
[SnaQ](https://www.ivistang.com/old/bioinfo/SNaQ%E8%BF%9B%E8%A1%8C%E7%B3%BB%E7%BB%9F%E5%8F%91%E8%82%B2%E7%BD%91%E6%9E%84%E5%BB%BA.html#%E8%BF%90%E8%A1%8C-ticr-%E6%B5%81%E7%A8%8B)  2025


# 备注--2026.05.09
当julia 包PhyloNetworks更新后导致版本过高时，以下流程中的部分命令有修改
可能需要加增安装SNAQ包
本人使用的版本为`PhyloNetworks v1.3.0`
```julia
using Pkg 
Pkg.add("SNaQ")
```
# 1. 安装
具体安装流程可以直接参考教程
```bash
# 1.use linux pkg

wget https://julialang-s3.julialang.org/bin/linux/x64/1.7/julia-1.7.2-linux-x86_64.tar.gz #下载linux的64位预编译的julia

tar -xzf julia-1.7.2-linux-x86_64.tar.gz #解压缩

julia-1.7.2/bin/julia -h #查看帮助文章


# 2. use julia github code

git clone https://github.com/JuliaLang/julia.git #克隆github上的最新版源代码

cd julia

git checkout v1.7.2 #运行checkout来获取julia的最新稳定版本1.7.2

make #编译

./julia -h #查看帮助文档
```

# 2. 准备基因树
一般来讲，由于phylonet运算时间较长，所以选取的物种数量一般较少，个人感觉控制在20往下较为合适
所以，如果事先系统发育分析建立的基因树中存在过多的物种，可以尝试提取部分物种来进行分析，即剪取子树
```bash
for tree in ../../05-final_genetrees_1outg/*.tre;do
    name=$(basename $tree .tre)
    echo $name
    gotree prune \
      -i $tree \
      -f spname_for_phylonet.txt \
      --revert \
      -o gene_tree/${name}_pruned_trees.tre
done

# 去除没有外类群或者物种数量太少的基因树,物种数量姑且设置为总数的一半
for tree in ./gene_tree/*.tre;do
    name=$(basename $tree .tre)
    nn=$(grep -c Outgroup $tree)
    mm=$(nw_labels -I $tree | wc -l)
    if [ $nn -eq 0 ] || [ $mm -le 8 ];then
        rm $tree
        echo -e "$name $nn $mm" >> rm_gene.txt
    fi
done

cat ./gene_tree/*.tre > alltree.rooted.txt
```

# 3. 准备CF表tableCF.csv
通过多基因树文件制备CF表tableCF.csv
```julia
using PhyloNetworks  
using CSV  
iqtrees=joinpath("alltree.rooted.txt") #读取多基因树文件  
genetrees = readMultiTopology(iqtrees) #解析基因树  
q,t = countquartetsintrees(genetrees) #读取基因树，计算四分类群的CFs  
df = tablequartetCF(q,t) #读取计算得到的CF值到df：基因频率  
CSV.write("tableCF.csv", df) #保存df内容为tableCF.csv文件
```

# 4. 批量运行不同的h值
将下列代码储存为runSNaQ_hn.jl
```julia
#!/usr/bin/env julia  
  
# file "runSNaQ.jl". run in the shell like this in general:  
# julia runSNaQ.jl hvalue nruns  
# example for h=2 and default 10 runs:  
# julia runSNaQ.jl 2  
# or example for h=3 and 50 runs:  
# julia runSNaQ.jl 3 50  
  
length(ARGS) > 0 ||  
    error("need 1 or 2 arguments: # reticulations (h) and # runs (optional, 10 by default)")  
h = parse(Int, ARGS[1])  
nruns = 10  
if length(ARGS) > 1  
    nruns = parse(Int, ARGS[2])  
end  
outputfile = string("net", h, "_", nruns, "runs") # example: "net2_10runs"  
seed = 1234 + h # change as desired! Best to have it different for different h  
@info "will run SNaQ with h=$h, # of runs=$nruns, seed=$seed, output will go to: $outputfile"  
  
using Distributed  
addprocs(nruns)  
@everywhere using PhyloNetworks  
net0_h6 = readTopology("rosa_orthofinder_MO_treeshrink_sp_rt_oneoutg_final.tre");  #读取起始树，为了避免并行时linux系统环境变量得区分，在h为1时设置为net0_h1  
using DataFrames, CSV  
df_sp = DataFrame(CSV.File("tableCF.csv", pool=false); copycols=false); #读取CF表  
d_sp = readTableCF!(df_sp);  
net_h6 = snaq!(net0_h6, d_sp, hmax=h, filename=outputfile, seed=seed, runs=nruns)
```

依次生成.jl文件后，直接运行下列bash文件，run_julia.sh
```bash
for h in $(seq 1 6); do 
  # 从h6模板复制并替换所有h6相关内容为当前h值 
  sed 's/h6/h'"${h}"'/g' runSNaQ_hn.jl > runSNaQ_h${h}.jl 
  echo "Generated runSNaQ_h${h}.jl" 
done

for h in $(seq 1 6);do 
    nohup julia runSNaQ_h${h}.jl ${h} > log_h${h}.log 2>&1 & 
done


```

批量运行
```bash 
screen -S julia  
ParaFly -c run_julia.sh -CPU 3
```

---

## 相关文献链接

- [[@2017]]
- [[@PhyloNetworks_ A Package for Phylogenetic Networks_2017]]
