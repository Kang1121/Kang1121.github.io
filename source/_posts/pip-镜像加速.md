---
title: pip 镜像加速
date: 2020-03-30 18:24:40
tags:
- 下载加速
categories:
- Linux
---

### &emsp;&emsp;1.&emsp;临时指定源

```bash
# xxxxxxx 为安装对象
pip install -i https://pypi.tuna.tsinghua.edu.cn/simple xxxxxxx
```

</br>

### &emsp;&emsp;2.&emsp;永久设定

```bash
 # Linux   下创建或进入
 # ~/.pip/pip.conf 
 [global]
 index-url = https://pypi.tuna.tsinghua.edu.cn/simple
 
 # Windows 下创建或进入
 # C:/User/xxxxxxx/pip/pip.ini
 [global]
 index-url = https://pypi.tuna.tsinghua.edu.cn/simple
```

