---
title: "HSA-RNN: Hierarchical Structure-Adaptive RNN for Video Summarization"
date: 2021-04-20 16:44:40
tags:
- LSTM
- Video Summarization
categories:
- Paper Reading
mathjax: true
---

### Overview

<div  align="center">  
<img src="/HSA-RNN-Hierarchical-Structure-Adaptive-RNN-for-Video-Summarization/1.png" width = "50%" height = "50%" alt="BiLSTM" align=center />
</div>

优点：frame-shot-video分层结构，双向LSTM滑动窗口

缺点：纯pixel features，supervised，importance score不新颖，滑动窗口k固定

兴趣程度：6

<!--more-->

### Understanding

中规中矩的一篇文章。提出了frame-shot-video的层级结构。先从frames中划分shots，再再划分的shots中，选择key shots。优点在于，单个shots的长度和shots的个数可变不固定，问题是默认了一个滑动窗口中必然出现shot boundary，这个是不一定的。选confidence score是用softmax的形式（n取1）。选shot boundary和key shot用的是一样的方法。
	
### Analysis

由于这篇文章需要label，标记正确key shots的位置来训练，所以还有待改进。说到底，还是没有真正找到frames（shots）在高维空间中的联系（loss function），所以只能有监督。