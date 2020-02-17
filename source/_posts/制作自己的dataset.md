---
title: 制作自己的 dataset
date: 2019-12-01 19:34:03
tags:
categories:
- Deep Learning
---

### &emsp;&emsp;制作自己的 dataset 要写一个自己的 Dataset 类，然后用 Dataloader  封装就好了。

---





# 一、Dataset 制作

### &emsp;&emsp;一般 Dataset 类有以下的函数：



```python
class Dataset(Dataset):

    def __init__(self, ...):
		...
    def __getitem__(self, index):
		...
    def __len__(self):
		...
	
```

</br>

### &emsp;&emsp;\_\_init\_\_() 是初始化时传入的一些参数，不是非常重要。

### &emsp;&emsp;\_\_getitem\_\_() 需要自己修改，功能是将图片从磁盘中读入，存入 array 或者 list 中。

### &emsp;&emsp;\_\_len()\_\_ 是返回 Dataset 的大小，需要仔细设计。

### &emsp;&emsp;下面是两个用于构建Siamese Nerual Network 的 example 。

### &emsp;&emsp;这个是事先将图片的 path 都写入了一个 txt 文件中，然后 getitem 就从 txt 中读图片路径。

```python
class MyDataset(Dataset):

    def __init__(self, txt, transform=None, target_transform=None, should_invert=False):

        self.transform = transform
        self.target_transform = target_transform
        self.should_invert = should_invert
        self.txt = txt

    def __getitem__(self, index):

        line = linecache.getline(self.txt, random.randint(1, self.__len__()))
        line.strip('\n')
        img0_list = line.split()
        # 判断是否要选同一个类别的图片
        should_get_same_class = random.randint(0, 1)
        if should_get_same_class:
            while True:
                # 随机在 txt 中选一行读取，每一行有一张图片的 path 和它的 label
                img1_list = linecache.getline(self.txt, random.randint(1, self.__len__())).strip('\n').split()
                if img0_list[1] == img1_list[1]:
                    break
        else:
            img1_list = linecache.getline(self.txt, random.randint(1, self.__len__())).strip('\n').split()

        img0 = Image.open(img0_list[0])
        img1 = Image.open(img1_list[0])
        # 转图片通道数
        img0 = img0.convert("L")
        img1 = img1.convert("L")

        # 非必要的
        if self.should_invert:
            img0 = PIL.ImageOps.invert(img0)
            img1 = PIL.ImageOps.invert(img1)

        # 非必要的
        if self.transform is not None:
            img0 = self.transform(img0)
            img1 = self.transform(img1)

        return img0, img1, torch.from_numpy(np.array([int(img1_list[1] != img0_list[1])], dtype=np.float32))

    # 这个 len 的返回长度就是 txt 中图片总数
    def __len__(self):
        fh = open(self.txt, 'r')
        num = len(fh.readlines())
        fh.close()
        return num
```

</br>

### &emsp;&emsp;这个是事先没有准备 txt 目录，所以写了一个 loadToMem 去每个文件夹里面找图片。

```python
class OmniglotTest(Dataset):

    def __init__(self, dataPath, transform=None, times=100, way=166):
        np.random.seed(1)
        super(OmniglotTest, self).__init__()
        self.transform = transform
        self.times = times
        self.way = way
        self.img1 = None
        self.c1 = None
        self.datas, self.num_classes = self.loadToMem(dataPath)

    def loadToMem(self, dataPath):
        print("begin loading test dataset to memory")
        # datas 是读取到的图片的总 list，idx 是类别数。
        datas = {}
        idx = 0
        for alphaPath in os.listdir(dataPath):
            # for charPath in os.listdir(os.path.join(dataPath, alphaPath)):
                datas[idx] = []
                for samplePath in os.listdir(os.path.join(dataPath, alphaPath)):
                    filePath = os.path.join(dataPath, alphaPath, samplePath)
                    s = Image.open(filePath).convert('L')
                    # 调整图片尺寸
                    s = s.resize((105, 105), Image.ANTIALIAS)
                    datas[idx].append(s)
                idx += 1
        print("finish loading test dataset to memory")
        return datas, idx

    def __len__(self):
        return self.times * self.way

    def __getitem__(self, index):
        idx = index % self.way
        label = None
        # generate image pair from same class
        if idx == 0:
            self.c1 = random.randint(0, self.num_classes - 1)
            self.img1 = random.choice(self.datas[self.c1])
            img2 = random.choice(self.datas[self.c1])
        # generate image pair from different class
        else:
            c2 = random.randint(0, self.num_classes - 1)
            while self.c1 == c2:
                c2 = random.randint(0, self.num_classes - 1)
            img2 = random.choice(self.datas[c2])

        if self.transform:
            img1 = self.transform(self.img1)
            img2 = self.transform(img2)
        # print('datas.shape = ', len(self.datas))
        # print('img1 = ', img1.shape)
        # print('img2 = ', img2.shape)
        return img1, img2

```

</br>

---



# 二、Dataloader 封装

### &emsp;&emsp;这个比较简单，因为 Dataloader 是 Pytorch 内置的函数。

```python
train_dataloader = DataLoader(dataset=train_data, shuffle=True, num_workers=2, batch_size=Config.train_batch_size)
```

</br>

### &emsp;&emsp;主要需要关注的是 batch_size 。这个以及之前提到的 len 和训练的次数有关。

```python
for i, data in enumerate(train_dataloader, 0):
```

</br>

### &emsp;&emsp;其中，for 循环的次数 = len / batch_size 。



