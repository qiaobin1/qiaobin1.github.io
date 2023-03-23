---
title: pandas高级数据处理学习笔记（简略版）
date: 2023-03-24 09:25:00
author: 乔彬
hide: false
summary: pandas高级数据处理学习笔记（简略版）
categories: 数据分析
tags:
  - 数据分析
  - 数据挖掘
---



## 一、缺失值处理

~~~python
import pandas as pd
1、判断是否存在缺失值
data=pd.read_csv('./1.csv')
pd.isnull(data).any() # 返回True说明存在缺失值
pd.notnull(data).all() # 返回False说明存在缺失值
2、缺失值是nan
data.dropna(inplace=True) # 删除
data['r'].fillna(data['r'].mean(),inplace=True) #填补
3、缺失值不是nan
data_new=data.replace(to_replace='?',value=np.nan)
data_new.dropna(inplace=True)
~~~

## 二、one-hot

~~~python
# 变成one-hot变量
sr=pd.Series([1,2,3,4,5,9,0],index=['x','y','c','v','b','n','m'])
# 分组
pd.qcut(sr,3)
# bins=[0,3,6,9]
# pd.cut(sr,bins)
# 转换
pd.get_dummies(sr,prefix='这是前缀')
~~~

## 三、合并

~~~python
# 按方向连接
data1=pd.concat([data1,data2],axis=0)
# 按索引连接
data2=pd.merge(left,right,how='inner',on=['key1','key2'])
~~~

## 四、交叉表

~~~python
# 交叉表
data=pd.crosstab(values1,values2)
data.div(data.sum(axis=1),axis=0)
~~~

## 五、分组与聚合

~~~python
# 对color列分组，price1进行聚合
df.groupby(by='color')['price1'].max()
~~~