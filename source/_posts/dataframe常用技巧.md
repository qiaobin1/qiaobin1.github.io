---
title: dataframe常用技巧
date: 2023-03-23 17:25:26
author: 乔彬
hide: false
summary: dataframe常用技巧
categories: 数据分析
tags:
  - 数据分析
  - 数据挖掘

---



## 1、os小结

os.getcwd()函数     #获得当前的路径

os.path.sep:       #路径分隔符 (相当于就是‘/’的作用）

os.path.join(os.getcwd(),'aaa', ‘bbb’, ‘ccc’)   拼接出来多级目录：E:\test\aaa\bbb\ccc

os.path.abspath(path) #返回绝对路径

os.path.basename(path) #返回文件名

~~~python
path =  os.path.abspath(os.getcwd())   # 获取当前路径
input_path = path+'/data/'             # 获取文件路径
featureName = ['carid', 'pushDate', 'pushPrice', 'updatePriceTimeJson', 'pullDate', 'withdrawDate']
Train_data = pd.read_csv(os.path.join(input_path,'附件4：门店交易训练数据.txt'), sep='\t', names=featureName)
~~~

## 2、取整函数，近似函数

~~~python
a=3.4
# 向下取整
b=int(a) #法1,b=3
import math
b=math.floor(a) #法2，b=3
# 向上取整
c=math.ceil(a) # c=4

# 四舍五入近似
df['car_age_year'] = round(df['car_age_day'] , 1) # 指的是在四舍五入到1位
df['car_age_year'] = df['car_age_day'].round(decimals=1) # 指的是在四舍五入到1位
~~~

## 3、处理目标值长尾分布

~~~python
#处理目标值长尾分布的问题
Train_data['carid'] = np.log1p(Train_data['carid']) # 取log
Train_data['carid'] = np.expm1(Train_data['carid']) # 用e还原

# 合并方便后面的操作
df = pd.concat([Train_data, Test_data], ignore_index=True)
~~~



## 4、dataframe中的transform

~~~python
df.groupby('order')['ext price'].sum()

"""
order	
10001     576.12	
10005    8185.49	
10006    3724.49	
Name: ext price, dtype: float64
"""

df.groupby('order')['ext price'].transform('sum') 

"""
0      576.12	
1      576.12	
2      576.12	
3     8185.49	
4     8185.49	
5     8185.49	
6     8185.49	
7     8185.49	
8     3724.49	
9     3724.49	
10    3724.49	
11    3724.49	
Name: ext price, dtype: float64
"""
~~~

## 5、dataframe日期数据处理

~~~python
data['withdrawDate'] = pd.to_datetime(data['withdrawDate'], format='%Y/%m/%d')
#  构造日期和月份特征
data['pushDateMonth'] = data['pushDate'].dt.month
data['pushDateYear'] = data['pushDate'].dt.year
df['regDate_day'] = df['regDate'].dt.day
df['car_age_day'] = (df['creatDate'] - df['regDate']).dt.day
~~~

## 6、pandas apply() 函数用法

pandas 的 `apply()` 函数可以作用于 `Series` 或者整个 `DataFrame`，功能也是自动遍历整个 `Series` 或者 `DataFrame`, 对每一个元素运行指定的函数。

### 6.1、Series.apply()

~~~python
df['ExtraScore'] = df['Nationality'].apply(lambda x : 5 if x != '汉' else 0) # 如果df['Nationality']不是汉族，对应位置为5，否则为0
df['NameLength'] = df['Name'].apply(len) # df['Name']的长度为
~~~

### 6.2、lambda 函数

~~~python
lambda x, y: x*y			# 函数输入是x和y，输出是它们的积x*y
~~~

### 6.3、DataFrame.apply()

~~~python
df.apply(np.square)   #整个df都做乘法运算
~~~

## 7、dataframe属性

~~~python
carid = df['carid'].values.tolist() # 转换为list式

values = df['carid'].values # 得到列表内容
index = df['carid'].index # 得到列表行索引
columns = df['carid'].columns # 得到列表列索引
shape = df['carid'].shape # 得到列表形状
dtype = df['carid'].dtype # 得到列表类型
~~~

## 8、astype函数用于array中数值类型转换

~~~python
# 适用于numpy，转换数据类型
x = np.array([1, 2, 2.5])
x1=x.astype(int)
~~~

## 9、dataframe分析相关性

~~~python
import pandas as pd

featureName = ['carid', 'pushDate', 'pushPrice', 'updatePriceTimeJson', 'pullDate', 'withdrawDate']
datanew = pd.read_csv(path+'附件4：门店交易训练数据.txt',sep='\t', names=featureName)

#  分析相关性
cor=datanew.corr(method='spearman')   # 相关性

pd.DataFrame(datanew.corr(method='spearman')).round(decimals=3) # round函数是4舍5入，decimals指的是在四舍五入到3位，即0.01245-》0.012
~~~
## 10、item（）的用法
作用：**取出单元素张量的元素值并返回该值**（保持原元素类型不变）
即：原张量元素为整形，则返回整形，原张量元素为浮点型则返回浮点型。