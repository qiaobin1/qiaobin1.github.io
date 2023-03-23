---
title: pandas基础学习笔记（简略版）
date: 2023-03-23 09:25:00
author: 乔彬
hide: false
summary: pandas基础学习笔记（简略版）
categories: 数据分析
tags:
  - 数据分析
  - 数据挖掘
---


## 1、DataFrame

既有行索引(index)又有列索引(columns)的二维数组

~~~python
# 生成dataframe
stock=[f'股票{i}' for i in range(10)]
date=pd.date_range(start='20211201',priods=5,freq='B')
data1=pd.DataFrame(stock_change,index=stock,columns=date)

data2=pd.DataFrame({'month':[1,2,3,45,5],
                  'year':[1,2,3,4,5],
                  'sale':[20,30,50,40,80]})

# dataframe的方法与属性
# 方法
data.head()
data.tail()
# 属性
data.columns
data.index
data.values
data.shape
data.dtype

# 重设索引
stock_=[f'股票_{i}' for i in range(10)]
data.index=stock_
# 扔掉原有的行索引，用1，2，3，4代替
data.reset_index(drop=Flase)
# 用‘股票0’列做索引
data.set_index('股票0'，drop=True)
~~~

## 2、series

带行索引的一维数组

~~~python
# 创建索引
data1=pd.Series({'red':10,'yellow':20,'black':40})
data2=pd.Series([1,2,3,4,5],index=['r','y','b','k','c'])
~~~

## 3、基本数据操作

1. 索引操作

   ~~~python
   data['columns名']
   data['columns名']['index名']
   data.iloc[0][0]
   data.loc['index名']['columns名']
   ~~~

2. 赋值操作

   ~~~python
   # 索引=值
   ~~~

3. 排序操作

   ~~~python
   data.sort_index(ascending=False)
   data.sort_values(by='行列名'，ascending=True)
   ~~~

## 4、DataFrame 运算

1. 算数运算

   和numpy中 **array与数运算**类似

   ~~~python
   data['股票0']+1
   data['股票1']*3
   ~~~

2. 逻辑运算

   ~~~python
   # 逻辑运算符  < > | &
   data['股票0']>0
   (data['股票0']>0)&(data['股票1']>0)
   # 逻辑运算函数
   data['股票0'].isin([3,2,1]) # 3,2,1在不在里面
   data.query('股票0>0&股票1<0')
   ~~~

3. 统计运算

   ~~~python
   # 1、df.describe()
   data.decribe()
   # 2、df.idxmax(axis=0)
   data.idxmax(axis=0)
   # 3、cumsum、cummax、cummin、cumprod(前n个数的积)
   data['股票0'].cumsum()
   ~~~

4. 自定义运算

   ~~~python
   # df.apply(func,axis=0)
   data.apply(lambda x: x.max()-x.min())
   ~~~

## 5、pandas绘图


~~~python

# 1、df.plot(x=none,y=none,kind='line')

data.plot(x="volume",y='turnover',kind='scatter')
# 2、Series.plot(kind='line')

sr.plot(kind='line')

~~~

## 6、文本的读取与存储

- csv

  ~~~python
  # 1、读取想要的列
  data=pd.read_csv('./1.csv',usecols=['列名'])
  # 2、给没有列名的图表加列名
  data=pd.read_csv('./2.csv',names=['列名'])
  # 3、保存文件
  data.iloc[:,0].to_csv('test.csv',index=False)
  data[0:5].to_csv('test1.csv',columns=['列名']，index=False)
  # 4、丢弃不想要的列
  data=data.drop(['ma','列名'，axis=1])
  ~~~

- hdf5

  ~~~python
  # hdf5文件读取与存储时需要指定一个键（类似matlab里的变量名）
  data=pd.read_hdf('./1.h5',key='变量名')
  data.iloc[,0:5].to_hdf("2.h5",key='变量名')
  ~~~

- json

  ~~~python
  data=pd.read_json('./1.json',orient='records',lines=True)
  data.iloc[:,1:5].to_json(('2.json',orient='records',lines=True))
  ~~~

  