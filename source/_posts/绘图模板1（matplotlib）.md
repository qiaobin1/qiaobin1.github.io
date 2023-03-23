---
title: 绘图模板1（matplotlib）
date: 2023-03-23 09:25:00
author: 乔彬
hide: false
summary: 绘图模板1（matplotlib）
categories: 数据分析
tags:
  - 数据分析
  - 数据挖掘
---

@[TOC](绘图模板（matplotlib）)
# 一、Matplotlib 是专门用于开发2D图表（三层结构）

1. 容器层
- 画板层Canvas
- 画布层Figure
- 绘图层/坐标系
2. 辅助显示层
3. 图像层


```python
import matplotlib.pyplot as plt
import numpy as np
import matplotlib as mpl
```

# 二、通用绘画模板--单折线图


```python
# 1.数据准备
x = range(60)
y= [random.uniform(15,18) for i in x]

# 2. 中文显示问题
plt.rcParams['font.sans-serif']=['SimHei']  # 用来正常显示中文标签
plt.rcParams['axes.unicode_minus']=False   # 用来正常显示负号

# 3、创建画布
plt.figure(figsize=(20,8),dpi=80)

# 4.绘制图像
plt.plot(x,y,color='r',linestyle='-.',label='上海')

# 5.显示图例
plt.legend(loc=1)

# 6.修改x y 刻度
plt.yticks(range(0,40,5))
plt.xticks(range(0,60,5))

# 7.显示网格
plt.grid(linestyle='--',alpha=0.5)

# 8.添加描述 标题
plt.xlabel('x_label')
plt.ylabel('y_label')
plt.title('title')

# 9.显示图像
plt.show()

```

![在这里插入图片描述](https://img-blog.csdnimg.cn/b7a72ab7659848678fc4dd8c67ce4e57.png)


​    


# 三、多坐标系绘图模板（两种形式）

## 法一：


```python
# 温度变化折线图
import matplotlib.pyplot as plt
%matplotlib inline
import random

# 1、准备数据
x = range(60)
y_shanghai = [random.uniform(15,18) for i in x]
y_beijing = [random.uniform(1,3) for i in x]

# 2、创建画布
# plt.figure(figsize=(20,8),dpi=80)
figure, axes = plt.subplots(nrows=1,ncols=2,figsize=(20,8),dpi=80)

# 3、绘制图像
axes[0].plot(x, y_shanghai, color="r",linestyle='-.',label="上海")
axes[1].plot(x, y_beijing, color="b",label="北京")

# 显示图例
axes[0].legend()
axes[1].legend()
# plt.legend(loc="lower left")
# plt.legend(loc=4)

# 修改x y刻度
x_label = ["11分{}秒".format(i) for i in x]
axes[0].set_xticks(x[::5])
axes[0].set_xticklabels(x_label[::5])
axes[0].set_yticks(range(0,40,5))
axes[1].set_xticks(x[::5])
axes[1].set_xticklabels(x_label[::5])
axes[1].set_yticks(range(0,40,5))

# 显示网格
axes[0].grid(linestyle='--', alpha=0.5)
axes[1].grid(linestyle='--', alpha=0.5)

# 添加描述 标题
axes[0].set_xlabel("时间")
axes[0].set_ylabel("温度")
axes[0].set_title("上海11点0分到12点之间的温度变化图示")

axes[1].set_xlabel("时间")
axes[1].set_ylabel("温度")
axes[1].set_title("北京11点0分到12点之间的温度变化图示")

# 4、显示图像
plt.show()
```


![image-20230323172511455](C:\Users\乔彬\AppData\Roaming\Typora\typora-user-images\image-20230323172511455.png)

​    

##  法二：


```python
# 温度变化折线图
import matplotlib.pyplot as plt
%matplotlib inline
import random

# 1、准备数据
x = range(60)
y_shanghai = [random.uniform(15,18) for i in x]
y_beijing = [random.uniform(1,3) for i in x]

# 2、创建画布
plt.figure(figsize=(20,8),dpi=80)

# 3、绘制图像1
plt.subplot(1,2,1)
plt.plot(x, y_shanghai, color="r",linestyle='-.',label="上海")

# 3.1.显示图例
plt.legend(loc=1)

# 3.2 修改x y 刻度
plt.yticks(range(0,40,5))
plt.xticks(range(0,60,5))

# 3.3显示网格
plt.grid(linestyle='--',alpha=0.5)

# 3.4添加描述 标题
plt.xlabel('x_label')
plt.ylabel('y_label')
plt.title('上海11点0分到12点之间的温度变化图示')

# 4、绘制图像2
plt.subplot(1,2,2)
plt.plot(x, y_beijing, color="b",label="北京")

# 4.1.显示图例
plt.legend(loc=1)

# 4.2 修改x y 刻度
plt.yticks(range(0,40,5))
plt.xticks(range(0,60,5))

# 4.3显示网格
plt.grid(linestyle='--',alpha=0.5)

# 4.4添加描述 标题
plt.xlabel('x_label')
plt.ylabel('y_label')
plt.title('北京11点0分到12点之间的温度变化图示')

# 5、显示图像
plt.show()
```

![在这里插入图片描述](https://img-blog.csdnimg.cn/da97c610eddd436a9fb86113658dcabb.png)

​    

​    


# 四、线属性的设置

1. 直接在plot（）函数中设置
2. 获得线属性，使用setp（）函数设置

其中常用的的参数有：

- xdata:需要绘制的line中点的在x轴上的取值，若忽略，则默认为range(1,len(ydata)+1)
- ydata:需要绘制的line中点的在y轴上的取值
- linewidth:线条的宽度
- linestyle:线型
- color:线条的颜色
- marker:点的标记，详细可参考markers API
- markersize:标记的size

## 直接在plot（）函数中设置


```python
x = range(0,5)
y = [2,5,7,8,10]
plt.plot(x,y, linewidth=10); # 设置线的粗细参数为10
```


![image-20230323172620869](C:\Users\乔彬\AppData\Roaming\Typora\typora-user-images\image-20230323172620869.png)
    


##  获得线属性，使用setp（）函数设置


```python
x = range(0,5)
y = [2,5,7,8,10]
lines = plt.plot(x, y)
plt.setp(lines, color='r', linewidth=10);
```


​    ![image-20230323172637726](C:\Users\乔彬\AppData\Roaming\Typora\typora-user-images\image-20230323172637726.png)



# 五、patches
- matplotlib.patches.Patch类是二维图形类，并且它是众多二维图形的父类
- 本小节重点讲述三种最常见的子类，矩形，多边形和楔型。

##  Rectangle-矩形

### hist-直方图
常用的参数：
- x: 数据集，最终的直方图将对数据集进行统计
- bins: 统计的区间分布
- range: tuple, 显示的区间，range在没有给出bins时生效
- density: bool，默认为false，显示的是频数统计结果，为True则显示频率统计结果，这里需要注意，频率统计结果=区间数目/(总数*区间宽度)，和normed效果一致，官方推荐使用density
- histtype: 可选{'bar', 'barstacked', 'step', 'stepfilled'}之一，默认为bar，推荐使用默认配置，step使用的是梯状，stepfilled则会对梯状内部进行填充，效果与bar类似
- align: 可选{'left', 'mid', 'right'}之一，默认为'mid'，控制柱状图的水平分布，left或者right，会有部分空白区域，推荐使用默认
- log: bool，默认False,即y坐标轴是否选择指数刻度
- stacked: bool，默认为False，是否为堆积状图


```python
# 1.准备数据
x=np.random.randint(0,100,100) #生成[0-100)之间的100个数据,即 数据集 
bins=np.arange(0,101,10) #设置连续的边界值，即直方图的分布区间[0,10),[10,20)... 
# 2、创建画布
plt.figure(figsize=(20,8),dpi=80)
# 3.绘制图形
plt.hist(x,bins,color='y',alpha=0.5)#alpha设置透明度，0为完全透明 
# 4.修改描述标题
plt.xlabel('scores') 
plt.ylabel('count') 
plt.xlim(0,100); #设置x轴分布范围 
# 5.显示图像
plt.show()
```


![image-20230323172648966](C:\Users\乔彬\AppData\Roaming\Typora\typora-user-images\image-20230323172648966.png)


### bar-柱状图
常用的参数：
- left：x轴的位置序列，一般采用range函数产生一个序列，但是有时候可以是字符串
- height：y轴的数值序列，也就是柱形图的高度，一般就是我们需要展示的数据；
- alpha：透明度，值越小越透明
- width：为柱形图的宽度，一般这是为0.8即可；
- color或facecolor：柱形图填充的颜色；
- edgecolor：图形边缘颜色
- label：解释每个图像代表的含义，这个参数是为legend()函数做铺垫的，表示该次bar的标签


```python
# 1.准备数据
x= range(0,16)
y= range(1,17) 
# 2、创建画布
plt.figure(figsize=(20,8),dpi=80)
# 3.绘制图形
plt.bar(x,y, alpha=0.5, width=0.5,color='y', edgecolor='red', label='The First Bar', lw=3)
# 4.修改描述标题
plt.xlabel('scores') 
plt.ylabel('count') 
plt.xlim(-1,16); #设置x轴分布范围 
# 5.显示图像
plt.show()
```


![image-20230323172700361](C:\Users\乔彬\AppData\Roaming\Typora\typora-user-images\image-20230323172700361.png)
    


##  Polygon-多边形（填充多边形）
matplotlib.patches.Polygon类中常用的是fill类，它是基于xy绘制一个填充的多边形，它的定义：

`matplotlib.pyplot.fill(args, data=None, *kwargs)`

- 参数说明 : 关于x、y和color的序列，其中color是可选的参数，每个多边形都是由其节点的x和y位置列表定义的，后面可以选择一个颜色说明符。您可以通过提供多个x、y、[颜色]组来绘制多个多边形。


```python
# 用fill来绘制图形
# 1、准备数据
x = np.linspace(0, 5 * np.pi, 1000) 
y1 = np.sin(x)
y2 = np.sin(2 * x) 
# 2、绘制画布
plt.figure(figsize=[20,8],dpi=80)
# 3、绘制图形
plt.fill(x, y1, color = "g", alpha = 0.5);
# 4、显示图形
plt.show()
```


![image-20230323172710705](C:\Users\乔彬\AppData\Roaming\Typora\typora-user-images\image-20230323172710705.png)
    


## Wedge-契形(饼图）
wedge中比较常见的是绘制饼状图。
制作数据x的饼图，每个楔子的面积用x/sum(x)表示。
其中最主要的参数是前4个：
- x：契型的形状，一维数组。
- explode：如果不是等于None，则是一个len(x)数组，它指定用于偏移每个楔形块的半径的分数。
- labels：用于指定每个契型块的标记，取值是列表或为None。
- colors：饼图循环使用的颜色序列。如果取值为None，将使用当前活动循环中的颜色。
- startangle：饼状图开始的绘制的角度。


```python
# 1、准备数据
labels="Frogs","hogs","dogs","logs"
sizes=[15,30,45,10]
explode=(0,0.1,0,0)
# 2、准备画布
plt.figure(figsize=(20,8),dpi=80)
# 3、绘制图形
plt.pie(sizes,explode=explode,labels=labels,autopct="%1.1f%%",shadow=True,startangle=90)
plt.axis('equal')  # 等宽高比确保饼图绘制为圆形
# 4、显示图片
plt.show()
```


![image-20230323172722479](C:\Users\乔彬\AppData\Roaming\Typora\typora-user-images\image-20230323172722479.png)

​    


# 六、collections（代表：散点图）
collections类是用来绘制一组对象的集合，collections有许多不同的子类，如RegularPolyCollection, CircleCollection, Pathcollection, 分别对应不同的集合子类型。其中比较常用的就是散点图，它是属于PathCollection子类，scatter方法提供了该类的封装，根据x与y绘制不同大小或颜色标记的散点图。 它的构造方法：

Axes.scatter(self, x, y, s=None, c=None, marker=None, cmap=None, norm=None, vmin=None, vmax=None, alpha=None, linewidths=None, verts=, edgecolors=None, , plotnonfinite=False, data=None, *kwargs)

其中最主要的参数是前5个：

- x：数据点x轴的位置
- y：数据点y轴的位置
- s：尺寸大小
- c：可以是单个颜色格式的字符串，也可以是一系列颜色
- marker: 标记的类型


```python
# 1、准备数据
x = [0,2,4,6,8,10] 
y = [10]*len(x)  
l=[0,1,2,3,4,5]        # 标签（类别）
s = [20*2**n for n in range(len(x))] 
# 2、准备画布
plt.figure(figsize=[20,8],dpi=80)
# 3、绘制散点图
plt.scatter(x,y,s=s,c=l,cmap=plt.cm.rainbow)
# 4、显示散点图
plt.show()
```


![image-20230323172732311](C:\Users\乔彬\AppData\Roaming\Typora\typora-user-images\image-20230323172732311.png)
    


# 七、images(代表：热图）
imshow根据数组绘制图像

`matplotlib.pyplot.imshow(X, cmap=None, norm=None, aspect=None, interpolation=None, alpha=None, vmin=None, vmax=None, origin=None, extent=None, shape=, filternorm=1, filterrad=4.0, imlim=, resample=None, url=None, , data=None, *kwargs）`

- 使用imshow画图时首先需要传入一个数组，数组对应的是空间内的像素位置和像素点的值，interpolation参数可以设置不同的差值方法，具体效果如下。


```python
# 1、准备数据
methods = [None, 'none', 'nearest', 'bilinear', 'bicubic', 'spline16',
           'spline36', 'hanning', 'hamming', 'hermite', 'kaiser', 'quadric',
           'catrom', 'gaussian', 'bessel', 'mitchell', 'sinc', 'lanczos']   # 插值方法
x=np.random.rand(4, 4)
# 2、准备画布
fig,axs=plt.subplots(nrows=3,ncols=6,figsize=(9,6),dpi=80)
# 3、绘制不同插值方法下的热图
for ax, method in zip(axs.flat,methods):
    ax.imshow(x,interpolation=method,cmap='rainbow')
    ax.set_title(str(method))
plt.tight_layout()  # 自动调节plt.subplot的间距
# 4、显示热图
plt.show()
```


​    
![image-20230323172741946](C:\Users\乔彬\AppData\Roaming\Typora\typora-user-images\image-20230323172741946.png)