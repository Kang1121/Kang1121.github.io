---
title: mAP CMC
date: 2020-02-12 19:53:29
tags:
categories: 
- Deep Learning
---

## mAP**(Mean Average Precision)**

### &emsp;&emsp;0.	TP & FN & TN & FP

#### &emsp;&emsp;&emsp;TP 是 true positive, 表示正样本被判断为正样本(即T, true). FN 是 false negative, 表示 negative sample 被判断为 positive sample (即F, false). 另外两个也一样.

### &emsp;&emsp;1.	Precision & Recall

#### &emsp;&emsp;&emsp;分类问题中, 现在已有每个样本的概率值和标签 (0, 1). 将样本按照概率从高到底排序. 分类器表现理想的情况是, 标签为 1 的样本都在上面, 标签为 0 的样本都在下面. 有个 threshold, 一开始指向最上面的 sample, 然后每次下移一个, threshold 及以上的都被认为是 positive sample, 下面的都被认为是 negative sample.

#### &emsp;&emsp;&emsp;Precision = TP / (TP + FN)	假设 TP + FN = k, precision 就表示前 k 个样本中, "negative sample 的比例", 因为它们本来应该呆在 positive sample 下面的, 但它们现在跑到 positive sample 上面去了. 有几个 positive sample 就计算几个 precision, threshold 每次下移一个, 当移到某个 sample 为 positive sample 的时候再算 precision.

#### &emsp;&emsp;&emsp;Recall = TP / (TP + FP) 	表示 positive sample 被成功找回来的概率. 因为 positive sample 总数不变, 所以 TP + FP 不变. Recall 最终会为1.

### &emsp;&emsp;2.	AP & mAP

#### &emsp;&emsp;&emsp;假设有

$$
AP =\frac{\sum^N_1 precision}{N}
$$

</br>

## CMC**(Cumulative Match Characteristics)**

### &emsp;&emsp;

</br>