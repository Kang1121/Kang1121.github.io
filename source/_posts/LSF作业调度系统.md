---
title: LSF 作业调度系统
date: 2020-03-24 10:50:44
tags:
- LSF
categories:
- Thinking
---

### 0. Intro

最近在搞 Vehicle Re-Id 的时候，要跑的模型太大，有幸用到了某单位的超算。用的是 LSF 作业调度系统。官方给的指南不太全，自己整理了几个常用的指令。

### 1. OS & Software

Windows 10 ( Linux 下没有 GUI，用 FTP 传文件还是不方便，用 windows 的话就是直接文件交互比较方便 )

软件用的是 MobaXterm。


### 2. Command List

#### bsub < lsf.sh

其中， lsf.sh 是自己写的一个脚本，之前犯错没输 <, 然后提交总是通不过。脚本范例如下：

```
# !/bin/sh
# BSUB -q gpu_v100
# BSUB -m "gpu10"
# BSUB -gpu num=4:mode=exclusive_process
# BSUB -o %J.out
# BSUB -n 1
# BSUB -e %J.err
# BSUB -J Firsttry
python3 /xxx/xxx/xxx/xxx.py
```

#### bjobs

该指令可以查看当前正在运行的作业。

![](/images/LSF作业调度系统/bjobs.jpg)


#### bjobs -l JOB_ID

该指令可以查看当前正在运行的作业的详细情况。

![](/images/LSF作业调度系统/bjobs-l.jpg)



#### bjobs -p JOB_ID

该指令可以查看作业 PENDING 的原因。

![](/images/LSF作业调度系统/bjobs-p.jpg)



#### bkill JOB_ID

该指令可以结束作业。

![](/images/LSF作业调度系统/bkill.jpg)



#### bkill -r JOB_ID

由于刚接触 LSF，不太了解运行机制。自己写了一些非常烂的程序，提交上去，导致怎么 bkill 都结束不了作业，然后输入 info bkill 查看了一下它的详细用法。这个可以直接杀死进程。

![](/images/LSF作业调度系统/bkill-r.jpg)



#### bstop JOB_ID** 与 **bresume JOB_ID

挂起和恢复作业，一般不用。如果作业不想继续了，最好直接 kill，否则占用资源。



#### bpeek JOB_ID 与 bpeek -f JOB_ID

该指令可以显示程序当前的屏幕输出。

![](/images/LSF作业调度系统/bpeek.jpg)


#### lsload 与 lsload -gpuload
该指令可以输出负载。

![](/images/LSF作业调度系统/lsload.jpg)



#### bhosts 与 bqueues

该指令可以查看各个节点和队列的作业信息。

![](/images/LSF作业调度系统/bhosts.jpg)

![](/images/LSF作业调度系统/bqueues.jpg)



### 3. Ending

最后祝自己以后还有机会用这样的 GPU 集群。:)