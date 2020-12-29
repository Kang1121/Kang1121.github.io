---
title: Learning on Attribute-Missing Graphs
date: 2020-12-29 00:19:02
tags:
- Paper Reading
categories:
- Deep Learning
mathjax: true
---

### 0. Introduction

&emsp;&emsp;Paper link: https://arxiv.org/pdf/2011.01623.pdf

&emsp;&emsp;Code link: https://github.com/xuChenSJTU/SAT-master-online

&emsp;&emsp;&emsp;&emsp;主要分析一下算法部分，实验部分不涉及。是VAE+GAN应用于graph的一个文章。用了两个VAE，然后用GAN对他们中间的高维分布Z进行加强。



### 1. VAE

&emsp;&emsp;&emsp;&emsp;VAE先推导一下。普通的AE只要求两个分布使得，输出=输入，即
$$
\hat x = x
$$


