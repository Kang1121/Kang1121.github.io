---
title: Brain-Controlled Robotic Arm System Based on Multi-Directional CNN-BiLSTM Network Using EEG Signals
date: 2021-06-30 13:25:02
tags: 
 - Robotic Arm
categories:
- Paper Reading
mathjax : true
---

### Overview

![](/Brain-Controlled-Robotic-Arm-System-Based-on-Multi-Directional-CNN-BiLSTM-Network-Using-EEG-Signals/1.png)

优点：regression的训练模式，第一次见

缺点：略微简单，没有subject independent

兴趣程度：7

<!--more-->


### Understanding

这篇文章的思路是，事先用实验数据拿到一个ground truth，然后train dataset去拟合这个gt。文章里面是这样说的：

> In our experiment, the robotic arm performed reaching tasks according to the directions given in order to acquire the baseline velocity profile after the sessions.
>
> Conventional hybrid deep learning frameworks have been commonly trained to extract spatial features of brain activities using a CNN and have been trained with temporal information using LSTM networks. In contrast, the proposed MDCBN framework was designed using a CNN architecture to train the multi-direction information per axis as **pretraining** and it used the BiLSTM network for training the **relationships** in the 3D space (x-, y-, and z-axes).

<div  align="center">  
<img src="/Brain-Controlled-Robotic-Arm-System-Based-on-Multi-Directional-CNN-BiLSTM-Network-Using-EEG-Signals/2.png" width = "50%" height = "50%" alt="BiLSTM" align=center />
</div>

所以CNN只是一个特征提取模块，然后用BiLSTM去获取xyz三个维度的关系，最后和gt做regression。


<div  align="center">  
<img src="/Brain-Controlled-Robotic-Arm-System-Based-on-Multi-Directional-CNN-BiLSTM-Network-Using-EEG-Signals/3.png" width = "50%" height = "50%" alt="BiLSTM" align=center />
</div>

### Analysis

这是第一篇涉及robotic arm的文章，还不知道这样的方法算不算新颖。还有一个问题就是，这个gt是由robotic arm自己运动产生的，然后让EEG信号通过网络，去拟合这个曲线，略微有点说不通。
