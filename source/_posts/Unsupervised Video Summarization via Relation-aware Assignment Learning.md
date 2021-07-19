---
title: Unsupervised Video Summarization via Relation-aware Assignment Learning
date: 2021-04-19 10:58:07
tags:
- GNN
- Video Summarization
categories:
- Paper Reading
---

### 0. Overview

<div  align="center">  
<img src="/images/Unsupervised Video Summarization via Relation-aware Assignment Learning/2.png" width = "70%" height = "70%" alt="BiLSTM" align=center />
</div>

优点：使用GNN来表达relation，unsupervised
缺点：clip的划分，loss的选取，纯pixel提取
兴趣程度：7

### 1. Understanding

将一个视频分成等长的n个clips，e.g. 10000帧的视频分成等长的20个clips，每个clip包含500帧。每个clip feature作为GNN中的一个node。node之间的edge关系，不是用邻接矩阵表示，而且用全连接的网络得到。node之间传递信息的时候，也是用全连接，把edge的信息当作先验，更新node。
MLP是为了进一步提取feature，经过MLP后，得到n个更加representative的feature clips。用L2 norm得到它们的importance score（这边应该是通过node间的联系紧密程度来得到的），然后取前k个作为summary embedding其他为non-summary embedding。另外GNN中所有node合起来得到video embedding，这三个embedding一起放入loss。用了triplet loss，想拉近v和s，拉远v和n。
	
### 2. Analysis

可部分参考。