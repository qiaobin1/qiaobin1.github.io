---
title: 菜鸟NumPy
date: 2023-03-23 09:25:00
author: 乔彬
hide: false
summary: 菜鸟NumPy
categories: 数据分析
tags:
  - 数据分析
  - 数据挖掘
---

## 一、Ndarray对象
**NumPy 最重要的一个特点是其 N 维数组对象 ndarray，它是一系列同类型数据的集合，以 0 下标为开始进行集合中元素的索引。**
```python
import numpy as np
# 语法
np.array(object,dtype=None,copy=True,order = None, subok = False, ndmin = 0)
# 实例
a=np.array([[1,2],[3,4]]) # 二维数组
b=np.array([1, 2, 3, 4, 5], ndmin =  2)  # 最小维度[[1 2 3 4 5]]
c = np.array([1,  2,  3], dtype = complex) # 数组元素数据类型 [1.+0.j 2.+0.j 3.+0.j]
```
## 二、数组属性
**2.1 Numpy基本类型**
- 有符号整型 int8、int16、int32、int64
- 无符号整型 uint8、uint16、uint32、uint64
- 浮点数 float16、float32、float64
- 复数 complex64、complex128
```python
import numpy as np

# 1、ndarray.ndim 用于返回数组的维数
a = np.arange(24) # [0,1,....,23]
print(a.ndim)   # 维度为1

# 2、ndarray.shape 用于返回数组的形状
print (a.shape)  # (24,1)

# 3、ndarray.itemsize 以字节的形式返回数组中每一个元素的大小。
# 数组的 dtype 为 int8（一个字节）
a = np.array([10,20,30], dtype = np.int8) 
print (a.itemsize) # 1
# 3、ndarray.dtype 用于返回数组元素的类型
print (a.dtype)    # int8
```
## 三、创建数组
### 3.1 简单创建数组
~~~ python
import numpy as np
# 1、np.empty(object,dtype) 创建一个3行2列随机数组
x = np.empty([3,2], dtype = int) 
# [[0, 0],
#  [0, 0],
#  [0, 0]]
# 2、创建指定大小的数组，数组元素以 0 来填充：
np.zeros([3,2], dtype = float)
# 3、 创建指定形状的数组，数组元素以 1 来填充
np.ones([3,2], dtype = None)
~~~
### 3.2、numpy从已有的数组创建数组
~~~python
import numpy as np 
x =  [(1,2,3),(4,5)] 
a = np.asarray(x, dtype =  float)  
print (a)
~~~
### 3.3、NumPy 从数值范围创建数组
~~~python
# 1、语法：
numpy.arange(start, stop, step, dtype)
import numpy as np 
x=np.arange(10,20,2) # [10  12  14  16  18]
# 2、语法：numpy.linspace 创建等差数列
np.linspace(start, stop, num=50, dtype=None)
a = np.linspace(10,20,5) # [10. 12. 14. 16. 18.]
# 3、numpy.logspace 函数用于创建一个于等比数列
np.logspace(start, stop, num=50, endpoint=True, base=10.0, dtype=None)
a = np.logspace(1.0,  2.0, num =  10)  
# [ 10.           12.91549665     16.68100537      21.5443469  27.82559402      
#  35.93813664   46.41588834     59.94842503      77.42636827    100.    ]
# lg10=1,lg100=2
~~~

## 四、切片和索引
- ndarray对象的内容可以通过索引或切片来访问和修改，与 Python 中 list 的切片操作一样。
- ndarray 数组可以基于 0 - n 的下标进行索引，切片对象可以通过内置的 slice 函数，并设置 start, stop 及 step 参数进行，从原数组中切割出一个新数组。
~~~ python
import numpy as np
# 基础索引
a = np.array([[1,2,3],[3,4,5],[4,5,6]])
b = a[2,2]   # 从索引 2 开始到索引 7 停止，间隔为 2
print(b)   # 6
# 获取数组中 (0,0)，(1,1) 和 (2,0) 位置处的元素
y = a[[0,1,2],  [0,1,0]]  
print (y)   # [1,4,4]
# 布尔索引
c=a[a> 2]
print(c)  # 大于2的元素是[3 3 4 5 4 5 6]
~~~
## 五、过滤
~~~ python
import numpy as np 
# 1、过滤 NaN
a = np.array([np.nan,  1,2,np.nan,3,4,5])  
print (a[np.isnan(a)])     # [nan nan]
print (a[~np.isnan(a)])   # [ 1.   2.   3.   4.   5.]
# 2、过滤复数元素
a = np.array([1,  2+6j,  5,  3.5+5j])  
print (a[np.iscomplex(a)]) # [2.0+6.j  3.5+5.j]
~~~
## 六、NumPy 广播(Broadcast)
对两个数组，分别比较他们的每一个维度
- 数组拥有相同形状
- 当前维度的值相等，另一维度的值是 1
~~~python
# 1、两个数组拥有相同形状
import numpy as np 
a = np.array([1,2,3,4]) 
b = np.array([10,20,30,40]) 
c = a * b  #[10,40,90,160]
print (c)
# 2、当前维度的值相等，另一维度的值是 1
a = np.array([[1,2,3,4],[2,3,4,5]]) 
b = np.array([10,20,30,40]) 
c = a * b 
print(c)  #[[10,40,90,160],[20,60,120,200]]
~~~
## 七、NumPy 迭代数组
numpy.nditer 提供了一种灵活访问一个或者多个数组元素的方式。
ndarray.flat 是一个数组元素迭代器
~~~python
import numpy as np
a = np.arange(6).reshape(2,3)
for x in np.nditer(a):
    print (x, end="," ) # 0,1,2,3,4,5,
for element in a.flat:
    print (element, end="," )# 0,1,2,3,4,5,
~~~
## 八、Numpy 数组操作
### 8.1 修改数组形状
~~~python
import numpy as np
a = np.arange(8)
# 1、ndarray.reshape(newshape, order='C')
b = a.reshape(4,2) 
# 2、ndarray.flatten(order='C') 变平
c = a.flatten(order='C')  order：'C' -- 按行，'F' -- 按列
~~~
### 8.2 翻转数组
~~~python
import numpy as np
# 1、numpy.transpose(arr, axes)
a = np.arange(12).reshape(3,4)
print (np.transpose(a))
# 2、ndarray.T 
print (a.T)
~~~
### 8.3 修改数组维度
~~~python
import numpy as np
a = np.arange(4).reshape(1,4)   # [[0 1 2 3]]
# 1、np.broadcast_to(array, shape) 数组广播到新形状
y=np.broadcast_to(a,(4,4))
print (y) # 数组广播到新形状
[[0 1 2 3]
 [0 1 2 3]
 [0 1 2 3]
 [0 1 2 3]]
# 2、numpy.expand_dims(arr, axis) 扩充形状
z = np.expand_dims(y, axis = 0) #shape(1,4,4)
# 3、numpy.squeeze(arr, axis)  删除一维的条目
l = np.squeeze(z)  # shape(4,4)
~~~
### 8.4 连接数组
~~~python
# 1、numpy.concatenate((a1, a2, ...), axis)
import numpy as np
a = np.array([[1,2],[3,4],[5,6]])    #(3,2)
b = np.array([[7,8],[9,10],[11,12]]) #(3,2)
# 沿轴 0 连接两个数组
c1 = np.concatenate((a,b),axis=0) #(6,2)
# 沿轴 1 连接两个数组
c2 = np.concatenate((a,b),axis=1) #(3,4)
# 2、numpy.stack(arrays, axis)
# 沿轴 0 堆叠两个数组
d1=np.stack((a,b),axis=0) #(2,3,2)
# 沿轴 1 堆叠两个数组
d2=np.stack((a,b),axis=1) #(3,2,2)
# 3、numpy.vstack(arrays)
# 沿轴 0 连接两个数组(竖直堆叠)
e1=np.vstack((a,b))       #(6,2)
# 4、numpy.hstack(arrays)
# 沿轴 1 连接两个数组(水平堆叠)
e2=np.hstack((a,b))       #(3,4)
~~~
### 8.5 分割数组
~~~python
# 1、numpy.split(array, indices_or_sections, axis)
import numpy as np
a=np.arange(16).reshape(4,4) 
# 沿轴 0 分割一个数组(竖直分割)
l1,l2=np.vsplit(a,2)       #(2,4)
b1,b2=np.split(a,2,axis=0) #(2,4)
# 沿轴 1分割一个数组(水平分割)
d1,d2=np.hsplit(a,2)       #(4,2)
c1,c2=np.split(a,2,axis=1) #(4,2)
~~~
### 8.6数组元素添加与删除
~~~python
# 1、返回指定形状的新数组-numpy.resize(arr, shape) 
import numpy as np
a=np.array([[1,2,3],[4,5,6]]) #(2,3)
b1=np.resize(a,(3,2))  #(3,2)
b2=np.reshape(a,(3,2)) #(3,2)
#[[1,2],[3,4],[5,6]]
# 2、函数在数组的末尾添加值-numpy.append(arr, values, axis=None)
c1=np.append(a,[7,8,9]) #[1, 2, 3, 4, 5, 6, 7, 8, 9]
# 沿轴 0 添加元素
c2=np.append(a,[[7,8,9]],axis=0) #(1,3)-->(3,3)
# 沿轴 1 添加元素
c3=np.append(a,[[7],[9]],axis=1) #(2,1)-->(2,4)
# 3、沿指定轴将值插入到指定下标之前-numpy.insert(arr, obj, values, axis)
d1=np.insert(a,3,[11,12]) # [ 1,  2,  3, 11, 12,  4,  5,  6]
# 沿轴 0 添加元素
d2=np.insert(a,1,[11],axis=0) #(3,3)
# [[ 1,  2,  3],[11, 11, 11],[ 4,  5,  6]]
# 沿轴 1 添加元素
d3=np.insert(a,1,[11],axis=1) #(2,4)
# 4、删掉某个轴的子数组，并返回删除后的新数组Numpy.delete(arr, obj, axis)
e1=np.delete(a,4)  # [1, 2, 3, 4, 6]
# 沿轴 0 删除元素
e2=np.delete(a,1,axis=0) #(1,3) [[1, 2, 3]]
# 沿轴 1 删除元素
e3=np.delete(a,1,axis=1) #(2,2) [[1,3],[4,6]]
# 沿切片删除元素
e4=np.delete(a,np.s_[::2]) # [2, 4, 6]
# 5、去除数组中的重复元素-numpy.unique(arr, return_index, return_inverse, return_counts)
a=[[11,11,11],[12,12,12]]
f1=np.unique(a) # [11, 12]
# 得到去重数组的原数组下标
f2,id1=np.unique(a,return_index=True) # [0, 3]
# 得到去重数组后的原数组下标
f3,id2=np.unique(a,return_inverse=True) # [0, 0, 0, 1, 1, 1]
# 使用下标重构原数组
a1=f3[id2] # [11, 11, 11, 12, 12, 12]
# 返回去重元素的重复数量
f4,id3=np.unique(a,return_counts=True) # [11, 12] --> [3, 3]

~~~