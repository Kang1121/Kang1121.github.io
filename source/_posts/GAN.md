---
title: GAN
date: 2020-02-17 00:27:51
tags:
categories:
- Deep Learning
---

## Generative Adversarial Network

### &emsp;0. Description

### &emsp;&emsp;&emsp;这篇 post 将所有了解过的 GAN 都写下来, 方便回忆原理.

</br>

### &emsp;1. GAN

### &emsp;&emsp;&emsp;最初 GAN 的目的是想 generate 出和真实图片非常像的图片. 有个有个图片集 data, 假设它在某维空间满足某个分布: 



<div  align="center">    
 <img src="/images/GAN/1.png" width = "50" height = "35" align=center />
</div>

### &emsp;&emsp;&emsp;但是现在不知道这个分布的 formula, 现在可以做的是从这个 data 的分布中, sample 出一些图片. Generator 就希望能通过 network 学到一个分布:

<div  align="center">    
 <img src="/images/GAN/2.png" width = "35" height = "35" align=center />
</div>

### &emsp;&emsp;&emsp;希望得到:

### 

<div  align="center">    
 <img src="/images/GAN/3.png" width = "120" height = "40" align=center />
</div>

### &emsp;&emsp;&emsp;也就是说, 当这两个分布相等时, 随便从 generator 学到的 distribution 中 sample 产生一个图片, 都会是在 data 的distribution 中的, 也就是说会是真实的图片.

### &emsp;&emsp;&emsp;因为 generator 是个 network, 所以它要做的就是学到一个 network, 使得这两个分布的 divergence 最小:

<div  align="center">    
 <img src="/images/GAN/4.png" width = "330" height = "35" align=center />
</div>

### &emsp;&emsp;&emsp;由于这两个分布它们的 formula 都不知道, 所以 divergence 其实是不能直接算的. 这个时候先把这个问题放一边, 看一下 discriminator.

</br>

### &emsp;&emsp;&emsp; Discriminator 要做的事情是区分真实的 image 和 generated 的 image. 如果是从 data distribution 中 sample 出来的, 就给高分; 如果是从 generated distribution 中 sample 出来的, 就给低分, 所以可以得到以下 cost function, 所以希望 V 越大越好, 已经经过了一部分等价转换:

<div  align="center">    
 <img src="/images/GAN/5.png" width = "550" height = "35" align=center />
</div>

