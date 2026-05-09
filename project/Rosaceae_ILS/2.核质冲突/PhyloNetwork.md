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

# 1. 安装
具体安装流程可以直接参考教程
```
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
一般来讲，由于phylonet运算时间较长，所以选取的物种数量一般较少，个人感觉控制在20往下较为合适。

# 3. 准备CF表tableCF.csv
通过多基因树文件制备CF表tableCF.csv
```
using PhyloNetworks  
using CSV  
iqtrees=joinpath("alltree.rooted.txt") #读取多基因树文件  
genetrees = readMultiTopology(iqtrees) #解析基因树  
q,t = countquartetsintrees(genetrees) #读取基因树，计算四分类群的CFs  
df = writeTableCF(q,t) #读取计算得到的CF值到df：基因频率  
CSV.write("tableCF.csv", df) #保存df内容为tableCF.csv文件
```

# 4. 批量运行不同的h值
将下列代码储存为runSNaQ_h1.jl
```
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
net0_h1 = readTopology("astral.tre");  #读取起始树，为了避免并行时linux系统环境变量得区分，在h为1时设置为net0_h1  
using DataFrames, CSV  
df_sp = DataFrame(CSV.File("tableCF.csv", pool=false); copycols=false); #读取CF表  
d_sp = readTableCF!(df_sp);  
net_h1 = snaq!(net0_h1, d_sp, hmax=h, filename=outputfile, seed=seed, runs=nruns)
```

依次生成.jl文件后，直接运行下列bash文件，run_julia.sh
```
julia runSNaQ_h1.jl 1 #数字为设置得h值
julia runSNaQ_h2.jl 2
julia runSNaQ_h3.jl 3
julia runSNaQ_h4.jl 4
julia runSNaQ_h5.jl 5
julia runSNaQ_h6.jl 6
```

批量运行
```
screen -S julia  
ParaFly -c run_julia.sh -CPU 3
```

---

## 相关文献链接

- [[@2017]]
- [[@PhyloNetworks_ A Package for Phylogenetic Networks_2017]]
