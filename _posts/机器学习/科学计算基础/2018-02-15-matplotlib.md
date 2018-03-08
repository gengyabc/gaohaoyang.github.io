---
layout: post
title:  "科学计算7 - Matplotlib"
categories: 科学计算
tags:  Matplotlib
author: Geng
---

* content
{:toc}

**Matplotlib**可能会是你从Matlab转向Python进行科学计算的决定因素，因为它与Jupyter结合真的是太好用了。它不仅带给你快速可视化数据的方法，还提供高质量的图表。

为了使用Matplotlib，在Jupyter笔记本所有代码之前，加一句`%matplotlib inline`，就可以开始使用了。








## 开始使用
Matplotlib的方法很像Matlab的画图方法，通过下面一行代码引入模块：
```python
from matplotlib import pyplot as plt
```

> 有时候你可能还会看到有人用`from pylab import *`，这是Matplotlib另一个接口，但是官方已经不推荐使用了

## 用例子说话
首先画几个简单的图，有一个直观的感觉。

所有工作之前，先引入需要的库


```python
import matplotlib.pyplot as plt
import numpy as np
%matplotlib inline
```

### 划线


```python
x = np.linspace(0,10,100)
y = np.exp(-x)
plt.plot(x, y)
plt.show()
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-02-15-matplotlib/output_5_0.png)


### 直方图


```python
x = np.random.normal(size=20000)
plt.hist(x, bins=100)
plt.show()
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-02-15-matplotlib/output_7_0.png)


### 散点图


```python
x = np.random.rand(100)
y = np.random.rand(100)
plt.scatter(x, y)
plt.show()
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-02-15-matplotlib/output_9_0.png)


## 画图设置

### 基本设置

首先我们画一个正弦和余弦的图


```python
X = np.linspace(-np.pi, np.pi, 256, endpoint=True)
C, S = np.cos(X), np.sin(X)

plt.plot(X, C)
plt.plot(X, S)

plt.show()
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-02-15-matplotlib/output_11_0.png)


我们可以看到，上图已经有了很多设置，比如线型，颜色等，说明默认设置已经帮我们做了许多工作，那么这些默认设置怎么做的呢？我们将这些默认设置显式的写出来看下(颜色可能不够准确，不要太讲究哈)。


```python
# 图片大小8x6英寸, 80 dpi(dots per inch)
plt.figure(figsize=(6, 4), dpi=80)

# 建立一个新的subplot，一行一列展示，以下是第一个其中的第一个图（这里是唯一的一个）
plt.subplot(1, 1, 1)

X = np.linspace(-np.pi, np.pi, 256, endpoint=True)
C, S = np.cos(X), np.sin(X)

# 蓝色cosine连续线型，线宽为1
plt.plot(X, C, color="blue", linewidth=1.0, linestyle="-")

# 橙色sine连续线型，线宽为1
plt.plot(X, S, color="orange", linewidth=1.0, linestyle="-")

# x轴范围
plt.xlim(-3.5, 3.5)

# x轴刻度线
plt.xticks(np.linspace(-3, 3, 7, endpoint=True))

# y轴范围
plt.ylim(-1.1, 1.1)

# y轴刻度线
plt.yticks(np.linspace(-1, 1, 9, endpoint=True))

# 也可以保存图片为72dpi
# plt.savefig("exercice_2.png", dpi=72)

# 展示图片
plt.show()
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-02-15-matplotlib/output_13_0.png)


上面说有设置自己修改，可以观察变化，学习怎样设置

如果想要增加图例的话，可以如下操作：


```python
plt.plot(X, C, color="blue", linewidth=2.5, linestyle="-", label="cosine")
plt.plot(X, S, color="red",  linewidth=2.5, linestyle="-", label="sine")

plt.legend(loc='upper left')
plt.show()
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-02-15-matplotlib/output_15_0.png)


### Figures和Subplots
Matplotlib中，**figure**是整个画布，在这个画布中有**subplot**。
subplot位置可以见下图：

![](http://www.scipy-lectures.org/_images/sphx_glr_plot_subplot-grid_001.png)

可以发现，前两个参数是画布大小（n * m个图），最后一个参数是具体位置。

## 更多方法请见文档
