---
title: cnn pytorch实现（从获取数据到训练模型)
date: 2023-03-26 06:25:00
author: 乔彬
hide: false
summary: cnn pytorch实现（从获取数据到训练模型)
categories: 深度学习
tags:
  - pytorch
  - 神经网络
---


## cnn整体框架（从获取数据到训练模型)

### 1、导入必要包

```
from torch.utils.data import DataLoader
import torchvision.transforms as transforms
from torchvision.utils import save_image
from scipy.io import loadmat 
import torch.utils.data as Data #导入data数据集
from torch.autograd import Variable
import matplotlib.pyplot as plt
import torchvision  #数据库模块
import numpy as np
import torch.nn as nn
import torch.nn.init as init
import torch
import torch.optim
import random
```



### 2、获取数据

```
# 设置路径
path='C:/Users/Desktop/fuse/代码/vibration data/' # 自己的路径，直接黏贴过来(注意反斜杠)
epoch=500 # 运行的次数
Batch_size=40 # 一次打包40个数据
# 加载m文件的获取数据方法
def data_load():
    # 训练集
    m=loadmat(path+'train1')   # m文件名
    train_s=m['train']  # m文件里的变量名
    train_x=train_s[:,0:400]  #我的m文件是（2400，401）加载前400行，数据
    train_y=train_s[:,400]-1  #第401列为标签（0到400=401），标签名从0开始，这里减一是因为我的数据集标签从1开始
    # 测试集
    m=loadmat(path+'test1')   # test1为m文件名
    test_s=m['test']  # m文件里的变量名
    test_x=test_s[:,0:400]   
    test_y=test_s[:,400]-1    
    return train_x,train_y,test_x,test_y
```

### 3、数据处理

```
# 加载数据
train_x_s1,train_y_s1,test_x_s1,test_y_s1=data_load()
# 打包生成浮点数张量
data=Data.TensorDataset(torch.FloatTensor(train_x_s1.reshape([2400,1,400])),
                        torch.FloatTensor(train_y_s1))   
# 为了一维卷积，所以要变换类型[2400,1,400],每个批次的装载数据都是3维的，维度构成batch_size,channel,height(N,C,H)
# 进行数据封装
train_loader=Data.DataLoader(dataset=data,batch_size=Batch_size,shuffle=True)
test_x=Varible(torch.FloatTensor(test_x_s1.reshape([1200,1,400]))) # 转换为浮点型张量
test_y=Variable(torch.LongTensor(test_y_s1))    # 转换为长实数张量
```

#### 1、生成张量

**TensorDataset** 可以用来对 tensor 进行打包，就好像 python 中的 zip 功能。

函数传入的参数必须是tensor类型，

即：data=Data.TensorDataset(tensor类型，tensor类型)

**torch.FloatTensor()**默认生成32位浮点数张量，dtype 为 `torch.float32` 或 `torch.float`

必须转化为浮点数，否则无法计算

#### 2、数据装载

数据载入后，我们还需要对数据进行装载；

理解：数据载入后，我们需要将这些图片打包好送给我们的模型进行训练，装载就是个打包的过程

train_loader=Data.DataLoader(dataset=data,batch_size=Batch_size,shuffle=True)

装载时通过batch_size的值来确认每个包的大小，通过shuffle（重新洗牌）的值确认是否在装载的过程中**打乱图片**的顺序(utils(常用工具))

#### 3、数据预处理

**pytorch两个基本对象：Tensor（张量）和Variable（变量）**

其中，tensor不能反向传播，variable可以反向传播。

**pytorch都是由tensor计算的，而tensor里面的参数是variable形式。**

**Variable**：一种可以不断变化的变量，符合反向传播，参数更新的属性。pytorch的variable是一个存放会变化值的地理位置，里面的值会不停变化。

### 4、定义模型

```
# 定义网络结构
class Model(nn.Module):
    def __init__(self):
        super(Model,self).__init__()
        self.layer1=nn.Sequential(
                nn.Conv1d(1,8,5), # 1代表1维
                nn.ReLU(),
                nn.MaxPool1d(5))#输出为[50,100,24]
        self.layer2=nn.Sequential(
                nn.Conv1d(8,16,3),
                nn.ReLU(),
                nn.MaxPool1d(3))  #这层的输出需要与下一层输入相同

        self.fc=nn.Sequential(nn.Linear(16*25,280),nn.ReLU(), # print(out.size())，显示当前输出的维度这里的结果是400，16维，25长
                              nn.Linear(280,180),nn.ReLU(),
                              nn.Linear(180,60),nn.ReLU(),
                              nn.Linear(60,30),nn.ReLU(),
                              )
        self.fc2=nn.Sequential(nn.Linear(30,12),nn.Softmax(dim=1)) # 12代表12类标签（0到11）
    def forward(self,x):
        out=self.layer1(x)
        out=self.layer2(out)
        out=out.view(out.size(0),-1)
        # print(out.size())#显示当前输出的维度
        out=self.fc(out)
        out=self.fc2(out)
        return out
```

除了上述的卷积开始的1以及之后全连接层的400以及全连接层的12以外，其他全部自定。

**卷积后输出图片大小：N = （W-F+2P）/ S +1**

W:输入图片，F:卷积核尺寸，S：步长, P: 填充

#### 0、nn.Sequential容器

```
torch.nn.Sequential(
            torch.nn.Conv2d(1,64,kernel_size=3,stride=1,padding=1),
            torch.nn.ReLU(),
            torch.nn.Conv2d(64,128,kernel_size=3,stride=1,padding=1),
            torch.nn.ReLU(),
            torch.nn.MaxPool2d(stride=2,kernel_size=2)
        )
```



一个有序的容器，**神经网络模块将按照在传入构造器的顺序依次被添加到计算图中执行**，同时以神经网络模块为元素的有序字典也可以作为传入参数。

#### 1、卷积

```
torch.nn.Conv1d(in_channels, out_channels, kernel_size, stride=1, padding=0, dilation=1, groups=1, bias=True)
```

out_channels(int) – 卷积产生的通道。有多少个out_channels，就需要多少个1维卷积 kernel_size(int or tuple) - 卷积核的尺寸，卷积核的大小为(k,)，第二个维度是由in_channels来决定的，所以实际上卷积大小为kernel_size*in_channels stride(int or tuple, optional) - 卷积步长 padding (int or tuple, optional)- 输入的每一条边补充0的层数 bias(bool, optional) - 如果bias=True，添加偏置

#### 2、激活

```
torch.nn.ReLU(inplace=False)
```

- inplace = False 时,**不会修改输入对象的值,而是返回一个新创建的对象**,所以打印出对象存储地址不同,类似于C语言的**值传递**
- inplace = True 时,会修改输入对象的值,所以打印出对象存储地址相同,类似于C语言的**址传递**

#### 3、最大池化

```
torch.nn.MaxPool2d(kernel_size, stride=None, padding=0, dilation=1, return_indices=False, ceil_mode=False)
```

**kernel_size(int or tuple)** - max pooling的窗口大小，

**stride(int or tuple, optional)** - max pooling的窗口移动的步长。默认值是kernel_size

**padding(int or tuple, optional)** - 输入的每一条边补充0的层数

**ceil_mode** - 如果等于True，计算输出信号大小的时候，会使用向上取整，代替默认的向下取整的操作

#### 4、全连接层

```
torch.nn.Linear(in_features,out_features,bias=True)
```

in_features--池化后的张量大小（c * h * w相乘)

out_features--全连接层的神经元个数

#### 5、Dropout层

```
torch.nn.Dropout(input, p=0.5, training=True, inplace=False)
```

其作用是，在 training 模式下，基于伯努利分布抽样，以概率 p 对张量 input 的值随机置0

**input：**输入张量

**p：**默认 0.5，张量元素被置0的概率；

**training：**默认 True，为 Ture 时执行dropout，为 False 时不执行，与[模块模式](https://blog.csdn.net/xiaoxiao_ziteng/article/details/114551912)设置相关；

**inplace：**默认 False，是否原地执行；

#### 6、softmax层

```
torch.nn.Softmax(dim=1)
```

![x=[1,2,3]\\ softmax(x)=[\frac{e^1}{e^1+e^2+e^3},\frac{e^2}{e^1+e^2+e^3},\frac{e^3}{e^1+e^2+e^3}]](https://latex.codecogs.com/gif.latex?x%3D%5B1%2C2%2C3%5D%5C%5C%20softmax%28x%29%3D%5B%5Cfrac%7Be%5E1%7D%7Be%5E1&plus;e%5E2&plus;e%5E3%7D%2C%5Cfrac%7Be%5E2%7D%7Be%5E1&plus;e%5E2&plus;e%5E3%7D%2C%5Cfrac%7Be%5E3%7D%7Be%5E1&plus;e%5E2&plus;e%5E3%7D%5D)![点击并拖拽以移动](data:image/gif;base64,R0lGODlhAQABAPABAP///wAAACH5BAEKAAAALAAAAAABAAEAAAICRAEAOw==)编辑



#### 7、前向传播

类内调用——self.xx

self.conv1(x)，进行卷积处理；

x.view(-1,14 * 14 * 28)扁平化

self.dense(x)，全连接层

return 分类

### 5、实例化以及定义

```
model=Model()   # 模型类实例化
print(model)

optimizer = torch.optim.Adam(model.parameters(), lr=LR)#定义优化器
loss_function =nn.CrossEntropyLoss()#定义损失函数，交叉熵损失函数
```

### 6、模型训练与参数优化

```
for epoch in range(EPOCH):
    for step, (x, y) in enumerate(train_loader): 
        batch_x = Variable(x) # pytorch里有两种数据类型，张量不能反向传播，为了反向传播，需要把张量x转换为variable
        batch_y = Variable(y)
        batch_x=batch_x.float() # 转换为浮点数
        batch_y=batch_y.long()  # 转换为长实数，为了配合底层模型训练以及参数优化

        #输入训练数据
        output = model(batch_x)

        #计算误差
        loss = loss_function(output, batch_y)
        #清空上一次梯度
        optimizer.zero_grad()
        #误差反向传递
        loss.backward()
        #优化器参数更新
        optimizer.step()
 # 用测试集验证模型       
    if epoch%10 == 0:
        test_output=model(test_x)
        pred_y=torch.max(test_output,1)[1].data.numpy().squeeze() 
        accuracy=sum(pred_y==test_y.numpy())/test_y.size(0) # 准确率
        # accuracy = torch.max(test_output, 1)[1].numpy() == test_y.numpy()
    
        print('Epoch:',epoch,'|Step:',step,'|train loss:%.4f'%loss.data.numpy(),
              '|test accuracy:%.4f'%accuracy)
```

torch.max(test_output,1)[1]——取最大的值对应的标签为预测类型

torch.max(test_output,1)[0]——最大的值，即代表此类为预测类

取到标签后用转换为numpy类型ndarray，便于后续处理

输出为：第几次，每次里需要运行多少步，训练集损失率，测试集准确率