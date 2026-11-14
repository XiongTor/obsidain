### <font style="color:#df2a3f;background-color:#fbde28;">17.</font><font style="color:rgb(31, 35, 40);">CTDGFinder：</font><font style="color:rgb(51, 51, 51);">识别注释基因组中的基因重复簇</font>






<font style="color:#df2a3f;background-color:#fbde28;">可视化软件：</font>**<font style="color:rgb(64, 184, 250);">geneviewer</font>**

学习链接：[https://mp.weixin.qq.com/s/NLHnT_SdcV715m5AURVMaA](https://mp.weixin.qq.com/s/NLHnT_SdcV715m5AURVMaA)

`<font style="color:rgb(53, 148, 247);">geneviewer</font>`<font style="color:rgb(43, 43, 43);">是一个用于绘制</font>`<font style="color:rgb(53, 148, 247);">基因簇</font>`<font style="color:rgb(43, 43, 43);">和</font>`<font style="color:rgb(53, 148, 247);">转录本</font>`<font style="color:rgb(43, 43, 43);">的</font><font style="color:rgb(43, 43, 43);"> </font>`<font style="color:rgb(53, 148, 247);">R</font>`<font style="color:rgb(43, 43, 43);"> </font><font style="color:rgb(43, 43, 43);">包。它从</font><font style="color:rgb(43, 43, 43);"> </font>`<font style="color:rgb(53, 148, 247);">GenBank</font>`<font style="color:rgb(43, 43, 43);">、</font>`<font style="color:rgb(53, 148, 247);">FASTA</font>`<font style="color:rgb(43, 43, 43);"> </font><font style="color:rgb(43, 43, 43);">和</font><font style="color:rgb(43, 43, 43);"> </font>`<font style="color:rgb(53, 148, 247);">GFF</font>`<font style="color:rgb(43, 43, 43);"> </font><font style="color:rgb(43, 43, 43);">文件导入数据，执行</font><font style="color:rgb(43, 43, 43);"> </font>`<font style="color:rgb(53, 148, 247);">BlastP</font>`<font style="color:rgb(43, 43, 43);"> </font><font style="color:rgb(43, 43, 43);">和</font><font style="color:rgb(43, 43, 43);"> </font>`<font style="color:rgb(53, 148, 247);">MUMmer</font>`<font style="color:rgb(43, 43, 43);"> </font><font style="color:rgb(43, 43, 43);">比对，并在基因箭头图上显示结果。该包提供广泛的自定义选项，</font>_<font style="color:rgb(255, 104, 39);">包括图例、标签、注释、比例、颜色、工具提示</font>_<font style="color:rgb(43, 43, 43);">等。要探索所有功能，请访问官网：</font>

+ [https://nvelden.github.io/geneviewer/index.html](https://nvelden.github.io/geneviewer/index.html)

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2025/png/54922767/1761265887592-7da1295d-8db8-4da1-ac2f-ce7ec0b07aa3.png)

**<font style="color:rgb(64, 184, 250);">用法</font>**

<font style="color:rgb(43, 43, 43);">下面的示例演示如何使用</font>`<font style="color:rgb(53, 148, 247);">geneviewer</font>`<font style="color:rgb(43, 43, 43);">在</font>`<font style="color:rgb(53, 148, 247);">基因组</font>`<font style="color:rgb(43, 43, 43);">序列上绘制</font>`<font style="color:rgb(53, 148, 247);">基因簇</font>`<font style="color:rgb(43, 43, 43);">，使用</font>`<font style="color:rgb(53, 148, 247);">每个基因的起始</font>`<font style="color:rgb(43, 43, 43);">和</font>`<font style="color:rgb(53, 148, 247);">终止</font>`<font style="color:rgb(43, 43, 43);">位置。基因按类别分组，并使用函数</font>`<font style="color:rgb(53, 148, 247);">GC_labels</font>`<font style="color:rgb(43, 43, 43);">添加标签。</font>

```plain
library(geneviewer)

# Data
gene_cluster <- data.frame(
  name = c("ophB1", "ophC", "ophA", "ophD", "ophB2", "ophP", "ophE"),
  start = c(2522, 5286, 9536, 12616, 13183, 19346, 20170),
  end = c(4276, 4718, 10904, 11859, 15046, 16016, 21484),
  class = c("Monooxygenase", "NTF2-like", "Methyltransferase", 
  "O-acyltransferase", "Monooxygenase", "Prolyloligopeptidase", 
  "F-box/RNHI-like")
)

# Chart
GC_chart(gene_cluster, group = "class", height = "100px") %>%
  GC_labels("name")
```

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2025/png/54922767/1761265998633-e8016ae9-3915-4c0d-9090-1a486050d618.png)

