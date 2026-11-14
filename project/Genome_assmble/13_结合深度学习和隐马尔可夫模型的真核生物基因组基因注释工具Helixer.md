### <font style="color:rgb(25, 27, 31);">13.结合深度学习和隐马尔可夫模型的真核生物基因组基因注释工具Helixer：</font>[https://github.com/weberlab-hhu/Helixer](https://github.com/weberlab-hhu/Helixer)
<font style="color:rgb(25, 27, 31);">在线版最大可以上传1个G的基因组，应该满足绝大部分的需求了，即使是大基因组也可以按染色体拆分上传</font>

<font style="color:rgb(25, 27, 31);">运行了网页版上的示例数据，22M的染色体，4分钟就跑完了</font>

学习参考链接：[https://zhuanlan.zhihu.com/p/682679905、[http://xuzhougeng.com/archives/helixer-solve-my-gene-annotation-problem](http://xuzhougeng.com/archives/helixer-solve-my-gene-annotation-problem)、[https://mp.weixin.qq.com/s/xtGtC3scWOzWpncPoYRFpw](https://mp.weixin.qq.com/s/xtGtC3scWOzWpncPoYRFpw)](https://zhuanlan.zhihu.com/p/682679905、[http://xuzhougeng.com/archives/helixer-solve-my-gene-annotation-problem](http://xuzhougeng.com/archives/helixer-solve-my-gene-annotation-problem)、[https://mp.weixin.qq.com/s/xtGtC3scWOzWpncPoYRFpw](https://mp.weixin.qq.com/s/xtGtC3scWOzWpncPoYRFpw))

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2025/png/54922767/1758877292630-125858c5-7831-40c1-87cd-5a27953484f0.png)

**网页版：**[**https://www.plabipd.de/helixer_main.html**](https://www.plabipd.de/helixer_main.html)

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2025/png/54922767/1758878422529-a042f874-285e-40f1-bb21-d812413b7c4c.png)

**<font style="color:rgb(25, 27, 31);">注释输出的</font>**[**<font style="color:rgb(9, 64, 142);">gff文件</font>**](https://zhida.zhihu.com/search?content_id=239836834&content_type=Article&match_order=1&q=gff%E6%96%87%E4%BB%B6&zd_token=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJ6aGlkYV9zZXJ2ZXIiLCJleHAiOjE3NTkwMzc3OTQsInEiOiJnZmbmlofku7YiLCJ6aGlkYV9zb3VyY2UiOiJlbnRpdHkiLCJjb250ZW50X2lkIjoyMzk4MzY4MzQsImNvbnRlbnRfdHlwZSI6IkFydGljbGUiLCJtYXRjaF9vcmRlciI6MSwiemRfdG9rZW4iOm51bGx9.c2KoW8aDNSbBTRnzqBnPP1Vbc7FbO_x4zmZnde0zRwM&zhida_source=entity)**<font style="color:rgb(9, 64, 142);">：/data/xueqin/Project/Prunus/Prunus_Geome_analyses/Prunus_zippeliana_genome/genome_annotation/Prunus_padus/padus_helixer.gff</font>**

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2025/png/54922767/1759839041895-72f589f3-f0d0-4e8f-bcb0-64715a4686b5.png)

```bash
从 gff 提取 CDS（核酸）并翻译成氨基酸序列
gffread padus_helixer.gff -g padusGCA_964059315.1_drPruPadu1.1_genomic.fna -x padus_cds.fa -y padus_proteins.fa

结果buco评估：
/home/xueqin/miniconda3/bin/busco -i padus_proteins.fa -c 1 -o busco_fasta -m prot -l /data/xueqin/Project/Prunus/Prunus_Geome_analyses/Prunus_zippeliana_genome/t2t/juicer/work/3d_dna_post/eudicots_odb10 -e 1e-05 --offline
```

<!-- 这是一张图片，ocr 内容为： -->
![](https://cdn.nlark.com/yuque/0/2025/png/54922767/1759044495058-2296219c-c97c-453d-918f-0971d340c64b.png)

