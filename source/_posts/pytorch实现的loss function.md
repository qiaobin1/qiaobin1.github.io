---
title: pytorch实现的loss function
date: 2023-03-21 15:25:00
author: 乔彬
hide: false
summary: pytorch实现的loss function
categories: 深度学习
tags:
  - pytorch
  - 神经网络
---


神经网络主要实现**分类以及回归预测**两类问题

对于分类，主要讲述**二分类交叉熵和多分类交叉熵函数**,对于回归问题，主要讲述**均方损失函数**，而对于一些回归问题，需要根据特殊情况**自定义损失函数**。

1、所有的loss的基类是Module，所以使用loss的方法是：

~~~python
# 1. 创建损失函数对象,并指定返回结果，默认为：平均值 以MSE为例
criterion = MSELoss(reduction='...')
# 2.  定义input x, traget y
x = torch.tensor(...) 
y = torch.tensor(...)
# 计算损失函数
loss = criterion(x, y)
~~~

**参数：**

reduction (string, optional）

- ‘none’: 不进行数据降维，输出为向量
- ‘elementwise_mean’： 将向量中的数累加求和，然后除以元素数量，返回**误差的平均值**
- ‘sum’： 返回向量的**误差值的和**

## 1.均方损失函数

 `1、class torch.nn.MSELoss(reduction='elementwise_mean') ` 默认返回**误差的平均值**

计算输入x和标签y，n个元素平均平方误差（mean square error），x和y具有相同的Size，**损失函数**如下定义：
![在这里插入图片描述](https://img-blog.csdnimg.cn/9439f3310e084e338390d3606bbafbc1.png)
如果reduction != ‘none’:
![在这里插入图片描述](https://img-blog.csdnimg.cn/3d65830d99214cbdbfbf79441b6a78ca.png)


通过代码来看一基本用法，以及reduction参数对返回结果的影响：

~~~python
import torch
from torch import nn

criterion_none = nn.MSELoss( reduction='none')
criterion_elementwise_mean = nn.MSELoss(reduction='elementwise_mean') 
criterion_sum = nn.MSELoss(reduction='sum')

x = torch.randn(3, 2, requires_grad=True)
y = torch.randn(3, 2)

loss_none = criterion_none(x, y)
loss_elementwise_mean = criterion_elementwise_mean(x, y)
loss_sum = criterion_sum(x, y )
print('reduction={}:   {}'.format('none', loss_none.detach().numpy()))
print('reduction={}:   {}'.format('elementwise_mean', loss_elementwise_mean.item()))
print('reduction={}:   {}'.format('sum', loss_sum.item()))


#%% 结果
reduction=none:
[[0.02320575 0.30483633]
[0.04768182 0.4319028 ]
[3.11864 7.9872203 ]]
reduction=elementwise_mean: 1.9855811595916748 # 1.9 * 6 = 11.4
reduction=sum: 11.913487434387207
~~~

## 2. 交叉熵损失函数

**如果目标值是onehot形式，那该函数之前先计算Sigmoid值\softmax值**

![在这里插入图片描述](https://img-blog.csdnimg.cn/7b0c2ed5a71c4f06896a3d9d90b89b7a.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP55m95a2m5Lmg6K6w5b2V,size_20,color_FFFFFF,t_70,g_se,x_16)


该博客讲述了交叉熵的定义以及为何使用交叉熵，对交叉熵不是很了解的可以看一下：
http://jackon.me/posts/why-use-cross-entropy-error-for-loss-function/

`2、class torch.nn.BCELoss(weight=None, reduction='elementwise_mean')`

- shape: N 为一批数据的数量

- input： ( N , ∗ ),  意味着任何数量的附加维度
  Target: (N,∗), shape与input相同

二分类的交叉熵函数。**使用该函数之前先计算Sigmoid值**。

损失函数表达式如下：
![在这里插入图片描述](https://img-blog.csdnimg.cn/8ad0481e102c49a4a3371c21282f4f52.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP55m95a2m5Lmg6K6w5b2V,size_20,color_FFFFFF,t_70,g_se,x_16)

~~~python
import torch
from torch import nn

m = nn.Sigmoid()
criterion = nn.BCELoss()
x = torch.randn(3, requires_grad=True)
# random_(from=0, to): 按均匀分布从[from, to -1]内去出离散整数值
# 将y取0或1
y = torch.empty(3).random_(2)
# 在计算前线计算x的sigmoid值
loss = criterion(m(x), y)
print(loss.item())

#%% result
0.8146645426750183
~~~

 `3、class torch.nn.CrossEntropyLoss(weight=None, size_average=None, ignore_index=-100, reduce=None, reduction='elementwise_mean')`

**在使用该函数前不需要经过softmax计算， target不是one_hot编码格式**

- shape: N是一批数据的数量

- input: (N, C): C是类的数量
  (N,C,d1,d2,…,dK) with K≥2 in the case of K-dimensional loss.
  Target： (N), 0≤targets[i]≤C−1,为每个样本的类别。
  (N,d1,d2,…,dK) with K≥2 in the case of K-dimensional loss
  Output: (N), (N, d1,d2,…,dK)

loss表达式：
![在这里插入图片描述](https://img-blog.csdnimg.cn/6607eaf5ed9d4316b2d5eb2972264720.png)

~~~python
import torch
from torch import nn

criterion = nn.CrossEntropyLoss()
x = torch.randn(3, 5, requires_grad=True)
# random_(from=0, to): 按均匀分布从[from, to -1]内去出离散整数值
# 将y取0或1
y = torch.empty(3, dtype=torch.long).random_(5)
# 在计算前线计算x的sigmoid值
loss = criterion(x, y)
print(loss.item())

#%% result
1.887318730354309
~~~

如果传给target是one_hot编码格式

多类的cross-entropy:
[0.1, 0.2, 0.7] (prediction) ------------------ [1.0, 0.0, 0.0] (target)

则损失函数为： - (1.0 * log(0.1) + 0.0 * log(0.2) + 0.0 * log(0.7))

~~~python
import torch
from torch import nn
from torch.nn import functional as F

x = torch.randn(3, 5, requires_grad=True)
# random_(from=0, to): 按均匀分布从[from, to -1]内去出离散整数值
# 将y取0或1
y = torch.empty(3, dtype=torch.long).random_(5)
def one_hot(y):
    '''
    y: (N)的一维tensor，值为每个样本的类别
    out: 
        y_onehot: 转换为one_hot 编码格式 
    '''
    y = y.view(-1, 1)
    y_onehot = torch.FloatTensor(3, 5)
    
    # In your for loop
    y_onehot.zero_()
    y_onehot.scatter_(1, y, 1)
    return y_onehot

# 1. 自带的函数
criterion = nn.CrossEntropyLoss()

# 2.将one_hot解码后使用pytorch自带的cross_entropy
def cross_entropy_one_hot(input, target):
    _, labels = target.max(dim=1)
    return F.cross_entropy(input, labels)  
    # 也可以使用以下语句
    # return nn.CrossEntropyLoss()(input, labels)


# 3. 自定义交叉熵函数
def cross_entropy(input_, target, reduction='elementwise_mean'):
    """ Cross entropy that accepts soft targets
    Args:
         pred: predictions for neural network
         targets: targets, can be soft
         size_average: if false, sum is returned instead of mean

    Examples::

        input = torch.FloatTensor([[1.1, 2.8, 1.3], [1.1, 2.1, 4.8]])
        input = torch.autograd.Variable(out, requires_grad=True)

        target = torch.FloatTensor([[0.05, 0.9, 0.05], [0.05, 0.05, 0.9]])
        target = torch.autograd.Variable(y1)
        loss = cross_entropy(input, target)
        loss.backward()
    """
    logsoftmax = nn.LogSoftmax(dim=1)
    res  =-target * logsoftmax(input_)
    if reduction == 'elementwise_mean':
        return torch.mean(torch.sum(res, dim=1))
    elif reduction == 'sum':
        return torch.sum(torch.sum(res, dim=1))
    else:
        return res

loss = criterion(x, y)
print("loss",loss.item())
loss_1 = cross_entropy_one_hot(x, one_hot(y))
print("loss_one_hot", loss_1.item())
loss_2 = cross_entropy(x, one_hot(y))
print("loss_custom", loss_2.item())

#%%result
loss 3.0437448024749756
loss_one_hot 3.0437448024749756
loss_custom 3.0437448024749756
~~~

## 3、自定义损失函数

### 1、关于nn.Module与nn.Functional的区别

**总结：**简答的说就是， nn.Module是一个包装好的类，具体定义了一个网络层，可以维护状态和存储参数信息；而nn.Functional仅仅提供了一个计算，不会维护状态信息和存储参数。

对于activation函数，比如（relu， sigmoid等），dropout，pooling等没有训练参数，可以使用functional模块。**两者的相同之处**：

- `nn.Xxx`和`nn.functional.xxx`的实际功能是相同的，即`nn.Conv2d`和`nn.functional.conv2d` 都是进行卷积，`nn.Dropout` 和`nn.functional.dropout`都是进行dropout，。。。。。； 
- 运行效率也是近乎相同。

**两者的不同之处：**

- `nn.functional.xxx`是函数接口，而`nn.Xxx`是`nn.functional.xxx`的类封装，并且**`nn.Xxx`都继承于一个共同祖先`nn.Module`。**这一点导致`nn.Xxx`除了具有`nn.functional.xxx`功能之外，内部附带了`nn.Module`相关的属性和方法，例如`train(), eval(),load_state_dict, state_dict `等。

- **两者的调用方式不同**

  `nn.Xxx` 需要先实例化并传入参数，然后以函数调用的方式调用实例化的对象并传入输入数据。

  ~~~python
  inputs = torch.rand(64, 3, 244, 244)
  conv = nn.Conv2d(in_channels=3, out_channels=64, kernel_size=3, padding=1)
  out = conv(inputs)
  ~~~

  `nn.functional.xxx`同时传入输入数据和weight, bias等其他参数。

  ~~~python
  weight = torch.rand(64,3,3,3)
  bias = torch.rand(64) 
  out = nn.functional.conv2d(inputs, weight, bias, padding=1)
  ~~~

  **`nn.Xxx`继承于`nn.Module`， 能够很好的与`nn.Sequential`结合使用， 而`nn.functional.xxx`无法与`nn.Sequential`结合使用。**

  ~~~python
  fm_layer = nn.Sequential(
              nn.Conv2d(3, 64, kernel_size=3, padding=1),
              nn.BatchNorm2d(num_features=64),
              nn.ReLU(),
              nn.MaxPool2d(kernel_size=2),
              nn.Dropout(0.2)
    )
  ~~~

  **`nn.Xxx`不需要你自己定义和管理weight；而`nn.functional.xxx`需要你自己定义weight，每次调用的时候都需要手动传入weight, 不利于代码复用。**

使用`nn.Xxx`定义一个CNN 。

~~~python
class CNN(nn.Module):
    
    
    def __init__(self):
        super(CNN, self).__init__()
        
        self.cnn1 = nn.Conv2d(in_channels=1,  out_channels=16, kernel_size=5,padding=0)
        self.relu1 = nn.ReLU()
        self.maxpool1 = nn.MaxPool2d(kernel_size=2)
        
        self.cnn2 = nn.Conv2d(in_channels=16, out_channels=32, kernel_size=5,  padding=0)
        self.relu2 = nn.ReLU()
        self.maxpool2 = nn.MaxPool2d(kernel_size=2)
        
        self.linear1 = nn.Linear(4 * 4 * 32, 10)
        
    def forward(self, x):
        x = x.view(x.size(0), -1)
        out = self.maxpool1(self.relu1(self.cnn1(x)))
        out = self.maxpool2(self.relu2(self.cnn2(out)))
        out = self.linear1(out.view(x.size(0), -1))
        return out
~~~

使用`nn.function.xxx`定义一个与上面相同的CNN。

~~~python
class CNN(nn.Module):
    
    
    def __init__(self):
        super(CNN, self).__init__()
        
        self.cnn1_weight = nn.Parameter(torch.rand(16, 1, 5, 5))
        self.bias1_weight = nn.Parameter(torch.rand(16))
        
        self.cnn2_weight = nn.Parameter(torch.rand(32, 16, 5, 5))
        self.bias2_weight = nn.Parameter(torch.rand(32))
        
        self.linear1_weight = nn.Parameter(torch.rand(4 * 4 * 32, 10))
        self.bias3_weight = nn.Parameter(torch.rand(10))
        
    def forward(self, x):
        x = x.view(x.size(0), -1)
        out = F.conv2d(x, self.cnn1_weight, self.bias1_weight)
        out = F.relu(out)
        out = F.max_pool2d(out)
        
        out = F.conv2d(x, self.cnn2_weight, self.bias2_weight)
        out = F.relu(out)
        out = F.max_pool2d(out)
        
        out = F.linear(x, self.linear1_weight, self.bias3_weight)
        return out
~~~

关于**dropout**，个人强烈推荐使用`nn.Xxx`方式，因为一般情况下只有训练阶段才进行dropout，在eval阶段都不会进行dropout。使用`nn.Xxx`方式定义dropout，在调用`model.eval()`之后，model中所有的dropout layer都关闭，但以`nn.function.dropout`方式定义dropout，在调用`model.eval()`之后并不能关闭dropout。

~~~python
class Model1(nn.Module):
    def __init__(self):
        super(Model1, self).__init__()
        self.dropout = nn.Dropout(0.5)
        
    def forward(self, x):
        return self.dropout(x)
    
    
class Model2(nn.Module):
    def __init__(self):
        super(Model2, self).__init__()
    def forward(self, x):
        return F.dropout(x)
    
m1 = Model1()
m2 = Model2()
inputs = torch.rand(10)
print(m1(inputs))
print(m2(inputs))
print(20 * '-' + "eval model:" + 20 * '-' + '\r\n')
m1.eval()
m2.eval()
print(m1(inputs))
print(m2(inputs))

#%%result
~~~
![在这里插入图片描述](https://img-blog.csdnimg.cn/79f6c1404e274340a8675bbf4497ffa3.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP55m95a2m5Lmg6K6w5b2V,size_20,color_FFFFFF,t_70,g_se,x_16)


**从上面输出可以看出`m2`调用了`eval`之后，dropout照样还在正常工作。**

### 2、自定义损失函数

只要Tensor算数操作（+， -，*， %，求导等）中，有一个Tesor
的resquire_grad=True,则该操作得到的Tensor具有反向传播，自动求导的功能。

因而只要自己实现的loss使用tensor提供的math operation就可以。

所以第一种自定义loss函数的方法就是使用tensor的math operation实现loss定义
继承于nn.Module
在forward中实现loss定义，注意：

所有的数学操作使用tensor提供的math operation
返回的tensor是0-dim的scalar
有可能会用到nn.functional中的一些操作https://www.zhihu.com/question/66988664

自定义MSEloss实现：

~~~python
class My_loss(nn.Module):
    def __init__(self):
        super().__init__()
        
    def forward(self, x, y):
        return torch.mean(torch.pow((x - y), 2))

 # 使用
criterion = My_loss()
loss = criterion(outputs, targets)
~~~

自定义entropyloss实现：

~~~python
# 3. 自定义交叉熵函数
def cross_entropy(input_, target, reduction='elementwise_mean'):   
    logsoftmax = nn.LogSoftmax(dim=1)
    res  =-target * logsoftmax(input_)
    if reduction == 'elementwise_mean':
        return torch.mean(torch.sum(res, dim=1))
    elif reduction == 'sum':
        return torch.sum(torch.sum(res, dim=1))
    else:
        return res
    """ Cross entropy that accepts soft targets
    Args:
         pred: predictions for neural network
         targets: targets, can be soft
         size_average: if false, sum is returned instead of mean

    Examples::

        input = torch.FloatTensor([[1.1, 2.8, 1.3], [1.1, 2.1, 4.8]])
        input = torch.autograd.Variable(out, requires_grad=True)

        target = torch.FloatTensor([[0.05, 0.9, 0.05], [0.05, 0.05, 0.9]])
        target = torch.autograd.Variable(y1)
        loss = cross_entropy(input, target)
        loss.backward()
    """
~~~