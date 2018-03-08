---
layout: post
title:  "科学计算11 - Pandas 可视化"
categories: 科学计算
tags:  Pandas
author: Geng
---

* content
{:toc}

有了 `Matplotlib`画图，数据展示方便了很多。但是如果想更方便呢？可以使用 `Pandas` 的画图功能, 直接 `df.plot()` 即可. `plot` 方法其实就是 `plt.plot()`的包装而已.






```python
import matplotlib.pyplot as plt
import pandas as pd
import numpy as np
%matplotlib inline

```

## Series

如果是 `Series` 数据, 不用说也知道干啥吧


```python
ts = pd.Series(np.random.randn(1000), index=pd.date_range('1/1/2000', periods=1000))
ts = ts.cumsum()
ts.plot()
```




    <matplotlib.axes._subplots.AxesSubplot at 0x22e50edd438>




![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-02-20-pandas-plot/output_3_1.png)


## DataFrame
对于 `DataFrame` 数据, `plot()` 可以很方便地画出各个列, 而且自带标签


```python
df = pd.DataFrame(np.random.randn(1000, 4), index=ts.index, columns=list('ABCD'))
df = df.cumsum()
df.plot()
```




    <matplotlib.axes._subplots.AxesSubplot at 0x22e51f694a8>




![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-02-20-pandas-plot/output_5_1.png)


如果想一列为 `x`, 一列为 `y`呢? 指定一下就好了:


```python
df3 = pd.DataFrame(np.random.randn(1000, 2), columns=['B', 'C']).cumsum()
df3['A'] = pd.Series(list(range(len(df))))
df3.plot(x='A', y='B')
```




    <matplotlib.axes._subplots.AxesSubplot at 0x22e50ed1630>




![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-02-20-pandas-plot/output_7_1.png)


## 其他图形
画图方法支持的其他图形很多, 这些方法可以通过 `plot(kind='some_plot_style')` 来实现, 这些支持的种类有:

* `bar` or `barh` for bar plots
* `hist` for histogram
* `box` for boxplot
* `kde` or `density` for density plots
* `area` for area plots
* `scatter` for scatter plots
* `hexbin` for hexagonal bin plots
* `pie` for pie plots

比如, bar plot 可以:


```python
df.iloc[5].plot(kind='bar')
```




    <matplotlib.axes._subplots.AxesSubplot at 0x10c9e7c50>




![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-02-20-pandas-plot/output_9_1.png)


也可以采用 `DataFrame.plot.<kind>` 这种形式, 这样寻找某个绘图方法就更简单了. 

除了上述方法, 还有 `DataFrame.hist()` 和 `DataFrame.boxplot()`, 不过它们使用了其它接口.


```python
df.iloc[5].plot.bar()
plt.axhline(0, color='k')
```




    <matplotlib.lines.Line2D at 0x10d162438>




![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-02-20-pandas-plot/output_11_1.png)



```python
df2 = pd.DataFrame(np.random.rand(10, 4), columns=['a', 'b', 'c', 'd'])
df2.plot.bar();
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-02-20-pandas-plot/output_12_0.png)



```python
df2.plot.barh(stacked=True);
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-02-20-pandas-plot/output_13_0.png)



```python

```
