---
title: 残差网络ResNet
date: 2023-03-23 11:25:56
author: 乔彬
hide: false
summary: 残差网络ResNet
categories: 深度学习
tags:
  - pytorch
  - 神经网络
---

## 一、定义

**残差指的是什么？**
其中ResNet提出了两种mapping：一种是identity mapping，指的就是图1中”弯弯的曲线”，另一种residual mapping，指的就是除了”弯弯的曲线“那部分，所以最后的输出是 y=F(x)+x
identity mapping顾名思义，就是指本身，也就是公式中的xx，而residual mapping指的是“差”，也就是y−x，所以残差指的就是F(x)部分。
**ResNet结构：**

<img src="https://img-blog.csdn.net/20180114184946861?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGFucmFuMg==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast" alt="img" style="zoom:50%;" />

它使用了一种连接方式叫做“shortcut connection”，顾名思义，shortcut就是“抄近道”的意思

**为什么ResNet可以解决“随着网络加深，准确率不下降”的问题？**
理论上，对于“随着网络加深，准确率下降”的问题，Resnet提供了两种选择方式，也就是identity mapping和residual mapping，如果网络已经到达最优，继续加深网络，residual mapping将被push为0，只剩下identity mapping，这样理论上网络一直处于最优状态了，网络的性能也就不会随着深度增加而降低了。

## 二、代码

<img src="https://img-blog.csdnimg.cn/20200514202531827.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDMzMTMwNA==,size_16,color_FFFFFF,t_70#pic_center" alt="img" style="zoom:50%;" />

~~~python
import torch.nn as nn
import torch.nn.functional as F

class basic_block(nn.Module):
    '''定义了带实线部分的残差块'''
    def __init__(self,in_channels):
        super(basic_block, self).__init__()
        self.conv1 = nn.Conv2d(in_channels,in_channels,kernel_size=3,stride=1,padding=1)
        self.conv2 = nn.Conv2d(in_channels,in_channels,kernel_size=3,stride=1,padding=1)
    def forward(self, x):
        y = F.relu(self.conv1(x))
        y = self.conv2(y)
        return F.relu(x+y)
~~~

***第二种是带有虚线标注的跳跃连接部分***，第一种结构是在通道数不变的情况下，进行的残差结构运算，第二种的跳跃连接结构，通道数发生了改变，于是把它单独做成一个基础块，如下图：

<img src="https://img-blog.csdnimg.cn/20200514201806710.jpg?x-oss-process=image/watermark,type_ZmFuZ3poZW5naGVpdGk,shadow_10,text_aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80NDMzMTMwNA==,size_16,color_FFFFFF,t_70#pic_center" alt="img" style="zoom:50%;" />

~~~python
class basic_block2(nn.Module):
    '''定义了带虚线部分的残差块'''
    def __init__(self,in_channels,out_channels):
        super(basic_block2, self).__init__()
        self.conv1 = nn.Conv2d(in_channels,out_channels,kernel_size=1,stride=2)
        self.conv2 = nn.Conv2d(in_channels,out_channels,kernel_size=3,stride=2,padding=1)
        self.conv3 = nn.Conv2d(out_channels,out_channels,kernel_size=3,stride=1,padding=1)
    def forward(self, x):
        z = self.conv1(x)
        y = F.relu(self.conv2(x))
        y = self.conv3(y)
        return F.relu(y+z)
~~~