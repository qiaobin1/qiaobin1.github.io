---
title: IEEE PHM 2012挑战赛的实验数据集
date: 2023-03-23 09:25:00
author: 乔彬
hide: false
summary: IEEE PHM 2012挑战赛的实验数据集
categories: 深度学习
tags:
  - 数据分析
  - 数据挖掘
---

轴承退化的表征基于传感器的两种数据类型：振动和温度；

## 1. 轴承退化：运行至故障试验

## 2.挑战数据集 

三种不同的工况如下，具体训练集和测试集特征如下所示

①：负载4000N，转速1800rpm;

②：负载4200N，转速1650rpm;

③：负载5000N，转速1500rpm;

![在这里插入图片描述](https://img-blog.csdnimg.cn/ed61978683a14397a93d965df89d1872.png?x-oss-process=image/watermark,type_d3F5LXplbmhlaQ,shadow_50,text_Q1NETiBA5bCP55m95a2m5Lmg6K6w5b2V,size_16,color_FFFFFF,t_70,g_se,x_16)


## 3.数据集介绍：

振动文件为acc_xxxxx.csv，温度文件为temp_xxxxx.csv

**振动信号(水平方向和垂直方向)：**

1.采样频率：25.6KHz；
2.采集设置：10秒采集一次，一次采集0.1s，即一次采集2560个点；示意图如下 温度信号 采样频率：10Hz;采集设置：每分钟采集600点

**每个文件的列标签：**

![在这里插入图片描述](https://img-blog.csdnimg.cn/8aaa7b33a37449288201e4b1fcd4dee8.png)



**温度信号：**

1.采样频率：10Hz;

2.采集设置：每分钟采集600点；

**注意：该数据为比赛数据，所有存放数据的文件夹有三个，一个是训练数据(Learning_set)，一个是比赛时采用的截断数据(Test_set)，一个是全寿命数据(Full_Test_set)，这个Full_Test_set是Test_set的完整版。**