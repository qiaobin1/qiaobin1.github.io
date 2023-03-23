---
title: pandas,matlab读取文件
date: 2023-03-23 09:25:00
author: 乔彬
top: true
hide: false
cover: true
summary: pandas,matlab读取文件
categories: 数据分析
tags:
  - 数据分析
  - 数据挖掘
---


## 一、pandas读取文件用法

### 1、pandas读取xlsx、xls文件

~~~python
import pandas as pd
data=pd.read_excel('path',sheetname='sheet1',header=0,names=['第一列','第二列','第三列'])
~~~

- path:要读取的文件的绝对路径

- sheetname：指定读取excel中的哪一个工作表，默认sheetname=0，即默认读取excel中的第一个工作表
  若sheetname = ‘sheet1’，即读取excel中的sheet1工作表；

- header:用作列名的行号，默认为header=0

  若header=None,则表明数据中没有列名行

  若header=0，则表明第一行为列名

- names:列名命名或重命名

### 2、pandas读取csv文件

~~~python
import pandas as pd
data=pd.read_csv('path',sep=',',header=0,names=["第一列"，"第二列"，"第三列"]，encoding='utf-8')
~~~

- path: 要读取的文件的绝对路径

- sep:指定列和列的间隔符，默认sep=','

  若sep=''\t",即列与列之间用制表符\t分割，相当于tab——四个空格

- header:列名行，默认为0

- names:列名命名或重命名

- encoding:指定用于unicode文本编码格式 

### 3、pandas读取txt文件

read_csv 也可以读取txt文件，读取txt文件的方法同上，也可以用read_table读取txt文件

~~~python
import pandas as pd
data = pd.read_table('path', sep = '\t', header = None, names = ['第一列','第二列','第三列'])
~~~

## 二、MATLAB的.mat文件与txt文件的相互转换（mat转txt，数据格式设置）

### 1、xx.txt转换为xx.mat

- load('路径\xx.txt')

  **加载txt文件**，加载成功之后，在workspace中出现与该txt文件同名的变量

  若txt文件名中有"-"字符，则在workspace中变量名中相应字符变为"_"

- save("路径\xx.mat","变量名")

~~~matlab
load("D:\matlabprogram\test-1.txt")
save('D:\matlabprogram\test-1.mat','test_1')
~~~

### 2、xx.mat转换为xx.txt

(1) 不考虑转换后txt文件中的数据格式

- load('路径\xx.mat')
- save('路径\xx.txt','','-ASCLL')

观察最后得到的txt文件内容发现，若存储的是数值序列，则txt中数值以可续计数法保存和显示。

（2）设置txt文件中的数据格式

~~~matlab
load("D:\matlabprogram\values.mat");
values.mafid = fopen('D:\matlabprogram\values.txt', 'wt');
fprint(fid,'%6.2f\n',values);
fclose(fid)
~~~

再次观察生成的txt文件会发现数值不再以科学计数法显示了，小数点保留了后两位。