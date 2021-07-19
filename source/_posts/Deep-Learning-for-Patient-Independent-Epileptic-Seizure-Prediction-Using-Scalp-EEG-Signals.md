---
title: >-
  Deep Learning for Patient-Independent Epileptic Seizure Prediction Using Scalp
  EEG Signals
date: 2021-07-01 15:19:13
tags:
 - Subject Independent BCI
categories:
- Paper Reading
mathjax : true
---

### Overview

<div  align=center>  
<img src="/Deep-Learning-for-Patient-Independent-Epileptic-Seizure-Prediction-Using-Scalp-EEG-Signals/1.png" width = "70%" height = "70%" alt="BiLSTM" align=center />
</div>


优点：通过对subject的身份判别来实现subject independent(SI)，很新颖，也抓住了问题的本质。

缺点：Siamese还不是终点，还可以通过别的方法去辨别不同的subject。

兴趣程度：9


<!--more-->


### Understanding

这篇文章提出了两个网络结构，第一个纯CNN，第二个是Siamese。做SI的方法是，通过训练网络，先判别某个EEG信号来自哪个subject。当网络训练好之后，就可以提取出每个subject的unique的characteristics。这对做SI很有帮助，SI是想让网络有泛化能力，针对不同subject各异的信号输入，都能给出相对一致(结果)的输出，可以对症下药。如果说网络已经获得了characteristics，那么下一步对症下药就会容易很多。比如说用GAN来生成具有common characteristic的信号，然后不断强化。比如说，输入是subject的信号，然后给他分析一波，unique的成分去掉，看看common的是什么东西，去做prediction。

<div  align=center>  
<img src="/Deep-Learning-for-Patient-Independent-Epileptic-Seizure-Prediction-Using-Scalp-EEG-Signals/2.png" width = "70%" height = "70%" alt="BiLSTM" align=center />
</div>

网络本身不复杂，但是这个思想感觉是很新颖，并且一针见血的。然后实验部分，有个SHAP模型，用来衡量信号各个component的贡献，可以参考。

<div  align=center>  
<img src="/Deep-Learning-for-Patient-Independent-Epileptic-Seizure-Prediction-Using-Scalp-EEG-Signals/3.png" width = "70%" height = "70%" alt="BiLSTM" align=center />
</div>

### Analysis
值得研究。


