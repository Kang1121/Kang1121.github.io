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

![](/images/Learning-on-Attribute-Missing-Graphs/structure.png)

&emsp;&emsp;Paper link: https://arxiv.org/pdf/2011.01623.pdf

&emsp;&emsp;Code link: https://github.com/xuChenSJTU/SAT-master-online

&emsp;&emsp;&emsp;&emsp;主要分析一下算法部分，实验部分不涉及。是VAE+GAN应用于graph的一个文章。用了两个VAE，分别训练X(attribute)和A(structure)。文章假设X和A的$z$在高维空间中遵从同一分布，然后用GAN对他们的高维分布$z$进行加强。



### 1. VAE

&emsp;&emsp;&emsp;&emsp;普通的AE只要，求两个分布p和q使得：
$$
\hat x \approx q(z)\\
z = p(x)
$$
但是实际上操作，有很多局限性。VAE对$z$做限制，使其遵从某个分布，这样如果直接sample一个这个分布给他作为$q(x)$的输入，就可以得到一个相对纯AE来说不错的结果。$p$和$q$是两个NN。

&emsp;&emsp;&emsp;&emsp;VAE先推导一下。手上有一堆观测数据$x$，现在要求他们的分布$p(x)$。由于分布可能非常复杂，这里用GMM来表示。假设$x$由一个隐变量$z$控制。那么有
$$
p(x)=\int_z p(x|z)p(z)
$$
接下来就是要求$p(x)$的MLE。即maximize
$$
L=\sum_x logp(x)
$$
做个恒等变形，
$$
\begin{equation}
\begin{split}
logp(x)&=\int_z q(z|x)log~p(x)dz=\int_z q(z|x)log\frac{p(x,z)}{p(z|x)}dz \\
&=\int_z q(z|x)log\frac{p(x,z)}{q(z|x)}dz+\int_z q(z|x)log\frac{q(z|x)}{p(z|x)}dz \\
&=\int_z q(z|x)log\frac{p(x,z)}{q(z|x)}dz +KL(q(z|x)||p(z|x))\\
&\ge \int_z q(z|x)log\frac{p(x,z)}{q(z|x)}dz=\int_z q(z|x)log\frac{p(x|z)p(z)}{q(z|x)}dz=L_b
\end{split}
\nonumber
\end{equation}
$$
因而，就是要maximize$L_b$，而
$$
\begin{equation}
\begin{split}
L_b&=\int_z q(z|x)log\frac{p(z)}{q(z|x)}dz+\int_z q(z|x)logp(x|z)dz\\
&=-KL(q(z|x)||p(z))+\int_z q(z|x)logp(x|z)dz
\end{split}
\nonumber
\end{equation}
$$
即minimize前者，maximize后者。前者只需训练 encoder $q$ 让他输出的分布 $z$ 接近 $p(z)$ 即可。后者可写成
$$
E_{x\sim q(z|x)}p(x|z)
$$
即普通AE的工作。

### 2. SAT

&emsp;&emsp;&emsp;&emsp;VAE+GAN的组合并不新鲜，但是这篇文章把它运用于重构图的节点信息，算是一个创新点。$X$表示的是每个node的attribute，$A$是整个图的structure。用两个VAE用于分别重构attribute和structure。其中，$E_x$、$D_x$、$D_A$是MLP，而$E_A$是GNN，其中的原因不是很懂。这篇文章是基于一个假设的，即：

> In graph structured data, each node’s attributes and structures are correlated together and can be represented in a shared-latent space.

因为如果不这样，就不能让两个VAE通过隐变量$z$互相学习了。不过这个假设背后的数学原理（是否有），目前还不懂，但这个假设是非常关键的一步。所以$z$其实是一个桥梁，充当了两个角色，1）$E_x->z->D_A$和$E_A->z->D_x$；2）$z_A$和$z_X$的分布用GAN去拟合。所以这个VAE+GAN和之前有篇VAE-GAN本质上不一样，之前的是用GAN去强化$x$和$\hat x$，使单个VAE的表现更好，图像更清晰，而这篇文章是用GAN去使两个$z$遵从同一分布。

&emsp;&emsp;&emsp;&emsp;最后贴一下损失函数作为总结，首先是VAE部分的，
$$
\begin{equation}
\begin{split}
min_{\theta_x,\theta_a,\phi_x,\phi_a}
L_r =&− E_{x_i\sim p_X} [E_{q_{\phi_x}(z_x|x_i)}[logp_{\theta_x}(x_i|z_x)]]\\
&− E_{a_i\sim p_A} [E_{q_{\phi_a}(z_a|a_i)}[log p_{\theta_a}(a_i|z_a)]]\\
&− λ_c · E_{a_i\sim p_A} [E_{q_{\phi_a}(z_a|a_i)}[log p_{\theta_x}(x_i|z_a)]]\\
&− λ_c · E_{x_i\sim p_X} [E_{q_{\phi_x}(z_x|x_i)}[log p_{\theta_a}(a_i|z_x)]]
\end{split}
\nonumber
\end{equation}
$$
其中，$\lambda_c$是调节上下两个的权重的。接下来是GAN的，
$$
\begin{equation}
\begin{split}
min_\psi max_{\phi_x,\phi_a} L_{adv} = &− E_{z_p\sim p(z)}[log D(z_p)]\\
&− E_{z_x\sim q_{\phi_x}(z_x|x_i)}[log(1 − D(z_x))]\\
&− E_{z_p\sim p(z)}[log D(z_p)]\\
&− E_{z_a\sim q_{\phi_a}(z_a|a_i)}[log(1 − D(z_a))]
\end{split}
\nonumber
\end{equation}
$$
其中，$\psi$是D的参数，$z_p$是label，来自某个分布，这里是Gaussian。最后是总的。
$$
min_\Theta max_\Phi L = L_r + L_{adv}
$$
其中，$\Theta = \{\theta_x, \theta_a, \phi_x, \phi_a, \psi\}$ ，$ Φ = \{\phi_x, \phi_a\}$。

