---
title: 将dataset标签写入txt
date: 2019-11-26 19:26:15
tags:
categories:
- Practical Codes
---

### &emsp;&emsp;从网上获取的数据集，想做成自己想要的样子，要为该数据集做一个 label.txt 方便以后的训练。比如像这样的，第一列是图片位置，第二列是标签：

</br>

![](/images/将dataset标签写入txt/1.png)

</br>

### &emsp;&emsp;因为我原先的这个数据集里面每类样本数不一样，所以稍微要烦一丢丢，直接上代码。

</br>

```python
import os
 
roots = "/home/yk/下载/VERI-Wild/images/"				


def convert(train=True):
    if train:
        f = open(roots + 'train.txt', 'w')			# 新建了一个train.txt存label的信息
        data_path = roots + 'images/'			
        if not os.path.exists(data_path):
            os.makedirs(data_path)
        label = 0
        for root, dirs, files in os.walk(data_path):
            if len(dirs):
                # print(dirs)
                continue
            else:
                label = label + 1
                s = len(files)
                for i in range(s):
                    img_path = root + '/' + files[i]
                    # print(img_path + ' ' + str(label))
                    f.write(img_path + ' ' + str(label) + '\n')			# 写入文件
        f.close()


convert(train=True)

```

