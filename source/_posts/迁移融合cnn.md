---
title: 迁移融合cnn
date: 2023-03-24 12:25:00
author: 乔彬
hide: false
summary: 迁移融合cnn
categories: 深度学习
tags:
  - 神经网络
  - pytorch
---

```python
# -*- coding: utf-8 -*-
"""
Created on Mon Nov  8 21:55:02 2021

@author: QB
"""

  
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
import os
import cv2
from numpy.matlib import repmat


''' 
#设备选择，如果有GPU则用GPU，否则则用CPU
MODEL_NAME = 'DANN'
DEVICE = torch.device("cuda" if torch.cuda.is_available() else "cpu")
'''


#2.三个网络的搭建
#2.1 搭建特征提取器网络
class Generator(nn.Module):
    def __init__(self):
        super(Generator,self).__init__()
        self.layer1=nn.Sequential(
                nn.Conv1d(5, 8, 3),
                nn.BatchNorm1d(8),
                nn.ReLU(),
                nn.MaxPool1d(2)#输出为[50,100,24]
                )
        self.layer2=nn.Sequential(
                nn.Conv1d(8,16,3),
                nn.BatchNorm1d(16),
                nn.ReLU(),
                nn.MaxPool1d(2))

        self.fc=nn.Sequential(nn.Linear(8160,680),nn.ReLU(),
                              nn.Linear(680,300),nn.ReLU(),
                              nn.Linear(300,56),nn.ReLU(),
                              nn.Linear(56,28),nn.ReLU(),
                              )
    def forward(self,x):
        out=self.layer1(x)
        # print(out.size())
        out=self.layer2(out)
        # print(out.size())
        out=out.view(out.size(0),-1)
        # print(out.size())#显示当前输出的维度
        out=self.fc(out)
        return out
    
    
#2.2 搭建分类器网络
class Classifier(nn.Module):
    """
        Classifier
    """
    def __init__(self, num_classes=5):
        super(Classifier, self).__init__()
        self.fc2 = nn.Sequential(
                nn.Linear(28,num_classes),
                nn.Softmax(dim=1))
        
    def forward(self, h):
        c=self.fc2(h)
        return c   
#2.3 搭建域判别网络
class Discriminator(nn.Module):
    """
        Simple Discriminator w/ MLP
    """
    def __init__(self, num_classes=1):
        super(Discriminator, self).__init__()
        self.layer = nn.Sequential(
                nn.Linear(28, num_classes),
                nn.Softmax(dim=1))
    
    def forward(self, h):
        y = self.layer(h)
        return y




# 读取图片文件
def data_load():
    vib_data2 = vib2_data[:,:,np.newaxis]
    vib_data3 = vib3_data[:,:,np.newaxis]



    data=[]
    for path1 in os.listdir(path):  # 得到路径path下的所有文件，返回list列表形式，返回的list列表顺序和path路径下的文件顺序是不一致
        new_path = path+path1+"/"
        print(new_path)

        for img1 in os.listdir(new_path):  # 得到路径path下的所有文件，返回list列表形式，返回的list列表顺序和path路径下的文件顺序是不一致
            img_path = new_path+img1
            image = cv2.imread(img_path)   # cv2.imread()用于读取图片文件，默认是1，读一副彩色3通道，imread函数有两个参数，第一个参数是图片路径，第二个参数表示读取图片的形式
            
            print(img_path)
            data.append(image) # 尾部插入图片数据
    data = np.array(data)   # 将数据转换为array，便于求平均值
    data = np.reshape(data, [4000, 2048,3])




    #################分别打乱两个数据集，划分训练集测试集##############
    image_train = []
    image_test = []
    vib_train2 = []
    vib_test2 = []
    vib_train3 = []
    vib_test3 = []
    rate = 0.6
    num = int(800*rate)
    num2 = 800-num
    for i in range(5):
        #打乱数据集
        temp1 = np.array(data[i*800:(i+1)*800,:,:])
        
        temp3 = np.array(vib_data2[i*800:(i+1)*800,:,:])
        temp4 = np.array(vib_data3[i*800:(i+1)*800,:,:])
        index = [i for i in range(len(temp1))] 
        random.shuffle(index)  # 打乱一维数组
        temp1 = temp1[index]
        
        temp3 = temp3[index]
        temp4 = temp4[index]
        
        #划分训练测试集
        image_train.append(temp1[0:num])
        image_test.append(temp1[num:])
        
        
        
        vib_train2.append(temp3[0:num])
        vib_test2.append(temp3[num:])
        
        vib_train3.append(temp4[0:num])
        vib_test3.append(temp4[num:])

    image_train = np.vstack((image_train[0],image_train[1],image_train[2],image_train[3],image_train[4]))   #  拼接，按垂直方向（行顺序）堆叠数组构成一个新的数组,堆叠的数组需要具有相同的维度
    image_test = np.vstack((image_test[0],image_test[1],image_test[2],image_test[3],image_test[4]))
              
    
    
    vib_train2 = np.vstack((vib_train2[0],vib_train2[1],vib_train2[2],vib_train2[3],vib_train2[4]))
    vib_test2 = np.vstack((vib_test2[0],vib_test2[1],vib_test2[2],vib_test2[3],vib_test2[4]))
    
    vib_train3 = np.vstack((vib_train3[0],vib_train3[1],vib_train3[2],vib_train3[3],vib_train3[4]))
    vib_test3 = np.vstack((vib_test3[0],vib_test3[1],vib_test3[2],vib_test3[3],vib_test3[4]))


    label_train = np.vstack([repmat(0,num,1),repmat(1,num,1),repmat(2,num,1),repmat(3,num,1),repmat(4,num,1)])     # numpy.repeat(a,repeats,axis=None) a等于原数组，repeats：复制次数，axis=0，1，默认为o，axis=1,沿着x轴复制，实际上增加列数
    label_test  = np.vstack([repmat(0,num2,1),repmat(1,num2,1),repmat(2,num2,1),repmat(3,num2,1),repmat(4,num2,1)])
    
    
    
    
    return image_train,  vib_train2, vib_train3, label_train, image_test,  vib_test2, vib_test3, label_test

# 数据处理
def dataProcessing(image_train,  vib_train2, vib_train3, label_train, image_test, vib_test2, vib_test3, label_test):
    # 打乱训练测试集
    np.random.seed(12)  # 随机数，随机数种子：12，需要每次调用都seed()一下，表示种子相同，从而生成的随机数相同,打乱方式相同。
    np.random.shuffle(image_train)

    np.random.seed(12)
    np.random.shuffle(vib_train2)
    np.random.seed(12)
    np.random.shuffle(vib_train3)
    np.random.seed(12)
    np.random.shuffle(label_train)

    np.random.seed(15)
    np.random.shuffle(image_test)

    np.random.seed(15)
    np.random.shuffle(vib_test2)
    np.random.seed(15)
    np.random.shuffle(vib_test3)
    np.random.seed(15)
    np.random.shuffle(label_test)

    label_train = np.reshape(label_train,[2400]).astype('int32')  # 改变数组格式，（2800，1）改变数组为（2800，）

    label_test  = np.reshape(label_test,[1600]).astype('int32')
    # TensorDataset对tensor进行打包
    train_loader = Data.DataLoader(dataset=Data.TensorDataset(torch.from_numpy(image_train).float(),

                                                              torch.from_numpy(vib_train2).float(),
                                                              torch.from_numpy(vib_train3).float(),
                                                              torch.from_numpy(label_train).float()),
                                   batch_size=batch_size, shuffle=False)
    test_x = Variable(torch.FloatTensor(np.concatenate([image_test, vib_test2, vib_test3], axis=2)))
    test_y = Variable(torch.LongTensor(label_test))
    return train_loader ,test_x,test_y



#2.4 类实例化，实例化成为具体的一个网络模型
F = Generator()#类实例化（实例化一个特征提取器）
C = Classifier()#类实例化（实例化一个分类器）
D = Discriminator()#类实例化（实例化一个域判别器）

# 超参数设定
max_epoch = 100
batch_size = 20
w = 64
h = 32
#第四类学习率
LR=4e-5
#2.5 设定损失函数和优化函数
bce = nn.BCELoss()
xe = nn.CrossEntropyLoss()

#2.5 定义三个优化器（优化参数和学习率）
F_opt=torch.optim.Adam(F.parameters(),lr=LR)
C_opt=torch.optim.Adam(C.parameters(),lr=LR)
D_opt=torch.optim.Adam(D.parameters(),lr=LR)

n_critic = 1 # for training more k steps about Discriminator

#载入源域
#################读取图片数据和振动数据##########################
main_path = "D:/data/migreadte data/Data of elder brother MAO paper"
path = main_path +"/infrared_image_dataToEnhance/L0/"
vib2_path = main_path+"/experimental data_800/fft/频域负载L0.mat"
vib2_data = loadmat(vib2_path)['Data_1_fft']
vib3_path = main_path+"/experimental data_800/square spectrum/平方谱负载L0.mat"
vib3_data = loadmat(vib3_path)['Data_1_spec']
image_train_s,  vib_train2_s, vib_train3_s, label_train_s, image_test_s, vib_test2_s, vib_test3_s, label_test_s=data_load()
train_loader_s ,test_x_s,test_y_s=dataProcessing(image_train_s,  vib_train2_s, vib_train3_s, label_train_s, image_test_s, vib_test2_s, vib_test3_s, label_test_s)


#载入目标域
path = main_path +"/infrared_image_dataToEnhance/L70/"
vib2_path = main_path+"/experimental data_800/fft/频域负载L70.mat"
vib2_data = loadmat(vib2_path)['Data_3_fft']
vib3_path = main_path+"/experimental data_800/square spectrum/平方谱负载L70.mat"
vib3_data = loadmat(vib3_path)['Data_3_spec']
image_train_t,  vib_train2_t, vib_train3_t, label_train_t, image_test_t, vib_test2_t, vib_test3_t, label_test_t=data_load()
train_loader_t ,test_x_t,test_y_t=dataProcessing(image_train_t,  vib_train2_t, vib_train3_t, label_train_t, image_test_t, vib_test2_t, vib_test3_t, label_test_t)

#4 训练过程 
def get_lambda(epoch, max_epoch):
    p = epoch / max_epoch
    return 2. / (1+np.exp(-10.*p)) - 1.

D_src = torch.ones(batch_size, 1) # Discriminator Label to real
D_tgt = torch.zeros(batch_size, 1) # Discriminator Label to fake
D_labels = torch.cat([D_src, D_tgt], dim=0)

for epoch in range(1, max_epoch+1):
    print('第',epoch,'次迭代','，共',max_epoch,'次')
    for (step, ((image_x_s,vib_x2_s,vib_x3_s, labels),(image_x_t,vib_x2_t,vib_x3_t,_))) in enumerate(
            zip(train_loader_s,train_loader_t)):
#        print(step)
        #对抗第一部分
        src=Variable(torch.concat([image_x_s,vib_x2_s,vib_x3_s],2)).float()
        src=src.transpose(1,2)
        tgt=Variable(torch.concat([image_x_t,vib_x2_t,vib_x3_t],2)).float()
        tgt=tgt.transpose(1,2)
        x=torch.cat([src, tgt], dim=0)
        h = F(x)#提取到的源域和目标域特征
        y = D(h.detach())#预测的域判别标签
#        print(y)
 
        Ld = bce(y, D_labels)#域判别损失函数
        
        D.zero_grad()
        Ld.backward()
        D_opt.step()
         
        #对抗第二部分
        c = C(h[:batch_size])#预测分类器标签
        y = D(h)#预测的判别器标签
        Lc = xe(c, labels.long())#分类损失函数
        Ld = bce(y, D_labels)#域判别损失函数
        lamda = 0.1*get_lambda(epoch, max_epoch)
        # Ltot = Lc -lamda*Ld#损失函数
        Ltot = Lc -0.01*Ld
        
        F.zero_grad()
        C.zero_grad()
        D.zero_grad()
        
        Ltot.backward()
        
        F_opt.step()
        C_opt.step()
        
        if step % 500 == 0:
            F.eval()
            C.eval()
            
#训练准确率
            
            c1=C(F(Variable(test_x_s.transpose(1,3))))
            _, preds1 = torch.max(c1, 1)
            pred_label1=preds1.numpy()
            accu=sum(pred_label1==test_y_s)/np.shape(test_y_s)[0]
            accu= torch.max(c1, 1)[1].numpy() == test_y_s.numpy()
            
            
#测试准确率
            
            c1=C(F(Variable(test_x_t.transpose(1,2))))
            _, preds = torch.max(c1, 1)
            pred_label=preds.numpy()
            # accuracy=sum(pred_label==test_y_t)/np.shape(test_y_t)[0]
            accuracy= sum(torch.max(c1, 1)[1].numpy() == test_y_t.numpy())/np.shape(test_y_t)[0]
            
            print('Epoch:',epoch,'|Step:',step,'|train loss:%.4f'%Ltot.data.numpy(),
                  '|test accuracy:%.4f'%accuracy,'|train accuracy:%.4f'%accu)
            
            F.train()
            C.train()
        step += 1
       


```