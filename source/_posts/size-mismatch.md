---
title: "RuntimeError: size mismatch, m1: [128 x 184320], m2: [9216 x 4096]"
date: 2019-11-30 16:42:55
tags: 
- Python错误
categories:
- Thinking
---

### 今天跑代码遇到这个问题，发现出错的位置是在卷积层到全连接层的地方。卷积网络的每层节点个数都是事先定好的，这对input的像素也有要求，必须也是和预先设定的像素相同，否则卷积层最后得出的结果会与设计的不同。

### 卷积层结果一般是 [batchsize, dimension0, 1, 1]， 全连接层只有二维 [batchsize, dimensio_0]，会自动对之前四维的卷积结果进行压缩。如果最后卷积结果不是 [batchsize, dimension_0, 1, 1] 的形式，就不会被压缩成 [batchsize, dimension_0] 的形式，而是别的形式。下面就算不下去了。

</br>

![](/images/size-mismatch/1.png)

</br>

### 像下图这个网络参数对应的输入图像尺寸应该是 105 x 105 的，但是我在预处理的时候没考虑到这点，虽然事后可以debug出来，但是应该事先要考虑到这点。

</br>

![](/images/size-mismatch/3.png)