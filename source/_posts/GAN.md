---
title: GAN
date: 2020-02-17 00:27:51
tags:
categories:
- Theoretical Analysis
---

### 0. Description

这篇 post 将所有了解过的 GAN 都写下来, 方便回忆原理.



### 1. GAN

最初 GAN 的目的是想 generate 出和真实图片非常像的图片. 有个有个图片集 data, 假设它在某维空间满足某个分布: 



<div  align="center">    
 <img src="/images/GAN/1.png" width = "50" height = "35" align=center />
</div>

但是现在不知道这个分布的 formula, 现在可以做的是从这个 data 的分布中, sample 出一些图片. Generator 就希望能通过 network 学到一个分布:

<div  align="center">    
 <img src="/images/GAN/2.png" width = "33" height = "33" align=center />
</div>

希望得到:


<div  align="center">    
 <img src="/images/GAN/3.png" width = "110" height = "38" align=center />
</div>

也就是说, 当这两个分布相等时, 随便从 generator 学到的 distribution 中 sample 产生一个图片, 都会是在 data 的distribution 中的, 也就是说会是真实的图片.

因为 generator 是个 network, 所以它要做的就是学到一个 network, 使得这两个分布的 divergence 最小:

<div  align="center">    
 <img src="/images/GAN/4.png" width = "370" height = "40" align=center />
</div>

由于这两个分布它们的 formula 都不知道, 所以 divergence 其实是不能直接算的. 这个时候先把这个问题放一边, 看一下 discriminator.


Discriminator 要做的事情是区分真实的 image 和 generated 的 image. 如果是从 data distribution 中 sample 出来的, 就给高分; 如果是从 generated distribution 中 sample 出来的, 就给低分: 

<div  align="center">    
 <img src="/images/GAN/7.png" width = "265" height = "45" align=center />
</div>

所以可以得到以下 cost function, 希望 V 越大越好, 已经经过了一部分等价转换:

<div  align="center">    
 <img src="/images/GAN/5.png" width = "570" height = "40" align=center />
</div>

把期望转成积分的形式:

<div  align="center">    
 <img src="/images/GAN/6.png" width = "550" height = "42" align=center />
</div>

预期是, 无论输入什么样的 x, 只要来自于 generator 就给低分, 只要来自于 real sample 就给高分, 所以其实 D of x 的在某处(e.g. x = 0)的值不受任何附近(e.g. (-1, 1))的值的影响, 完全可以看成是一元函数求极值. 

<div  align="center">    
 <img src="/images/GAN/8.png" width = "270" height = "53" align=center />
</div>

最后算出来, 理论上 D 取这个值的时候 V 可以达到最大, 也就是说 discriminator 的性能最好. 但是两个分布都不知道, 所以没有解析解, 真正操作时这个用不了. 但是把上式代入 V 中, 整理后可得:

<div  align="center">    
 <img src="/images/GAN/9.png" width = "600" height = "50" align=center />
</div>

发现这个正好是 JS divergence 的形式. 又由于最初要求的就是 g 和 data distribution 的 divergence, 所以把上式代入 G star 得:

<div  align="center">    
 <img src="/images/GAN/10.png" width = "320" height = "52" align=center />
</div>

这个式子就可以用 network 求了. 先固定 Generator 训练 Discriminator 让 V 达到 max, 再固定 Discriminator  训练 Generator 使得 V 取最小值. Iteratively 训练.



```
import argparse
import os
import numpy as np
import math

import torchvision.transforms as transforms
from torchvision.utils import save_image

from torch.utils.data import DataLoader
from torchvision import datasets
from torch.autograd import Variable

import torch.nn as nn
import torch.nn.functional as F
import torch

os.makedirs("images", exist_ok=True)

parser = argparse.ArgumentParser()
parser.add_argument("--n_epochs", type=int, default=200, help="number of epochs of training")
parser.add_argument("--batch_size", type=int, default=64, help="size of the batches")
parser.add_argument("--lr", type=float, default=0.0002, help="adam: learning rate")
parser.add_argument("--b1", type=float, default=0.5, help="adam: decay of first order momentum of gradient")
parser.add_argument("--b2", type=float, default=0.999, help="adam: decay of first order momentum of gradient")
parser.add_argument("--n_cpu", type=int, default=8, help="number of cpu threads to use during batch generation")
parser.add_argument("--latent_dim", type=int, default=100, help="dimensionality of the latent space")
parser.add_argument("--img_size", type=int, default=28, help="size of each image dimension")
parser.add_argument("--channels", type=int, default=1, help="number of image channels")
parser.add_argument("--sample_interval", type=int, default=400, help="interval betwen image samples")
opt = parser.parse_args()
print(opt)

img_shape = (opt.channels, opt.img_size, opt.img_size)

cuda = True if torch.cuda.is_available() else False


class Generator(nn.Module):
    def __init__(self):
        super(Generator, self).__init__()

        def block(in_feat, out_feat, normalize=True):
            layers = [nn.Linear(in_feat, out_feat)]
            if normalize:
                layers.append(nn.BatchNorm1d(out_feat, 0.8))
            layers.append(nn.LeakyReLU(0.2, inplace=True))
            return layers

        self.model = nn.Sequential(
            *block(opt.latent_dim, 128, normalize=False),
            *block(128, 256),
            *block(256, 512),
            *block(512, 1024),
            nn.Linear(1024, int(np.prod(img_shape))),
            nn.Tanh()
        )

    def forward(self, z):
        img = self.model(z)
        img = img.view(img.size(0), *img_shape)
        return img


class Discriminator(nn.Module):
    def __init__(self):
        super(Discriminator, self).__init__()

        self.model = nn.Sequential(
            nn.Linear(int(np.prod(img_shape)), 512),
            nn.LeakyReLU(0.2, inplace=True),
            nn.Linear(512, 256),
            nn.LeakyReLU(0.2, inplace=True),
            nn.Linear(256, 1),
            nn.Sigmoid(),
        )

    def forward(self, img):
        img_flat = img.view(img.size(0), -1)
        validity = self.model(img_flat)

        return validity


# Loss function
adversarial_loss = torch.nn.BCELoss()

# Initialize generator and discriminator
generator = Generator()
discriminator = Discriminator()

if cuda:
    generator.cuda()
    discriminator.cuda()
    adversarial_loss.cuda()

# Configure data loader
os.makedirs("../../data/mnist", exist_ok=True)
dataloader = torch.utils.data.DataLoader(
    datasets.MNIST(
        "../../data/mnist",
        train=True,
        download=True,
        transform=transforms.Compose(
            [transforms.Resize(opt.img_size), transforms.ToTensor(), transforms.Normalize([0.5], [0.5])]
        ),
    ),
    batch_size=opt.batch_size,
    shuffle=True,
)

# Optimizers
optimizer_G = torch.optim.Adam(generator.parameters(), lr=opt.lr, betas=(opt.b1, opt.b2))
optimizer_D = torch.optim.Adam(discriminator.parameters(), lr=opt.lr, betas=(opt.b1, opt.b2))

Tensor = torch.cuda.FloatTensor if cuda else torch.FloatTensor

# ----------
#  Training
# ----------

for epoch in range(opt.n_epochs):
    for i, (imgs, _) in enumerate(dataloader):

        # Adversarial ground truths
        valid = Variable(Tensor(imgs.size(0), 1).fill_(1.0), requires_grad=False)
        fake = Variable(Tensor(imgs.size(0), 1).fill_(0.0), requires_grad=False)

        # Configure input
        real_imgs = Variable(imgs.type(Tensor))

        # -----------------
        #  Train Generator
        # -----------------

        optimizer_G.zero_grad()

        # Sample noise as generator input
        z = Variable(Tensor(np.random.normal(0, 1, (imgs.shape[0], opt.latent_dim))))

        # Generate a batch of images
        gen_imgs = generator(z)

        # Loss measures generator's ability to fool the discriminator
        g_loss = adversarial_loss(discriminator(gen_imgs), valid)

        g_loss.backward()
        optimizer_G.step()

        # ---------------------
        #  Train Discriminator
        # ---------------------

        optimizer_D.zero_grad()

        # Measure discriminator's ability to classify real from generated samples
        real_loss = adversarial_loss(discriminator(real_imgs), valid)
        fake_loss = adversarial_loss(discriminator(gen_imgs.detach()), fake)
        d_loss = (real_loss + fake_loss) / 2

        d_loss.backward()
        optimizer_D.step()

        print(
            "[Epoch %d/%d] [Batch %d/%d] [D loss: %f] [G loss: %f]"
            % (epoch, opt.n_epochs, i, len(dataloader), d_loss.item(), g_loss.item())
        )

        batches_done = epoch * len(dataloader) + i
        if batches_done % opt.sample_interval == 0:
            save_image(gen_imgs.data[:25], "images/%d.png" % batches_done, nrow=5, normalize=True)

```

