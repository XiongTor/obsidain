---
title: "PyNCBIminer批量抓取下载叶绿体数据"
date: "2026-10-27"
authors: Tao Xiong
tags:
---
### Github
[PyNCBIminer_NG](https://github.com/wyx619/PyNCBIminer_NG) 最新版本，以下流程也是基于这个版本进行
[PyNCBIminer](https://github.com/Xiaoting-Xu/PyNCBIminer)
### 官方作者的论文中的相关流程图
下图为官方作者的一次交流中提供的参考流程图，最终结果并不一定以此为准，但以此为参考，制作了本次流程

![|325](../../imag/PyNCBIminer_NG批量抓取下载叶绿体数据/ref.png)

# 一 . 直接使用图形化界面操作

# 二. 使用源码操作
本流程主要使用源码，主要是下载的数据量可能较大，不在windows的本地电脑上运行了

## 1. 下载源码并进行环境配置
```bash
## github仓库克隆
git clone https://github.com/wyx619/PyNCBIminer_NG.git /data/PyNCBIminer_NG

## Linux 环境配置，基于conda进行

```