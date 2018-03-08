---
layout: post
title:  "科学计算12 - Seaborn 入门"
categories: 科学计算
tags:  Pandas
author: Geng
---

* content
{:toc}


在这个 **Seaborn** 简介中，将学习如何使用 **Python** 最方便的库之一来进行数据可视化。

对于那些曾经使用过 **Matplotlib** 的人来说，你可能想知道，为什么需要10行代码才能制作出像样的直方图？

如果你正在寻找一个更简单的方式来绘制有吸引力的图表，那么你会爱上 **Seaborn**。






[The Ultimate Python Seaborn Tutorial: Gotta Catch ‘Em All](https://elitedatascience.com/python-seaborn-tutorial)

## 介绍

**Seaborn** 为 **Matplotlib** 提供了一个高级接口，**Matplotlib** 是一个功能强大但有时很笨重的 **Python** 可视化库。

**Seaborn** 的官方网站表示：

>如果 matplotlib “试图使简单的事情保持简单, 困难的事情成为可能”，seaborn 试图让一套明确定义的困难事情变得容易。

这是对 **Seaborn** 强项的不错总结。在实践中，“一套明确定义的困难事情”包括：


* 使用美观的默认主题。
* 设置自定义调色板。
* 制作有吸引力的统计图。
* 轻松灵活地显示分布。
* 可视化来自矩阵和 `DataFrame` 的信息。

最后三点就是为什么 **Seaborn** 是我们探索性分析的首选工具。这使得快速高效地“了解”数据非常容易。

然而，**Seaborn 是 Matplotlib 的补充，而不是替代品**。还有一些特殊设置仍然需要 **Matplotlib**。

## 开始

首先[下载数据](https://elitedatascience.com/wp-content/uploads/2017/04/Pokemon.csv)


```python
import pandas as pd
from matplotlib import pyplot as plt
%matplotlib inline
import seaborn as sns
```


```python
# Read dataset
df = pd.read_csv('data/Pokemon.csv', index_col=0)
df.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Name</th>
      <th>Type 1</th>
      <th>Type 2</th>
      <th>Total</th>
      <th>HP</th>
      <th>Attack</th>
      <th>Defense</th>
      <th>Sp. Atk</th>
      <th>Sp. Def</th>
      <th>Speed</th>
      <th>Stage</th>
      <th>Legendary</th>
    </tr>
    <tr>
      <th>#</th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
      <th></th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>1</th>
      <td>Bulbasaur</td>
      <td>Grass</td>
      <td>Poison</td>
      <td>318</td>
      <td>45</td>
      <td>49</td>
      <td>49</td>
      <td>65</td>
      <td>65</td>
      <td>45</td>
      <td>1</td>
      <td>False</td>
    </tr>
    <tr>
      <th>2</th>
      <td>Ivysaur</td>
      <td>Grass</td>
      <td>Poison</td>
      <td>405</td>
      <td>60</td>
      <td>62</td>
      <td>63</td>
      <td>80</td>
      <td>80</td>
      <td>60</td>
      <td>2</td>
      <td>False</td>
    </tr>
    <tr>
      <th>3</th>
      <td>Venusaur</td>
      <td>Grass</td>
      <td>Poison</td>
      <td>525</td>
      <td>80</td>
      <td>82</td>
      <td>83</td>
      <td>100</td>
      <td>100</td>
      <td>80</td>
      <td>3</td>
      <td>False</td>
    </tr>
    <tr>
      <th>4</th>
      <td>Charmander</td>
      <td>Fire</td>
      <td>NaN</td>
      <td>309</td>
      <td>39</td>
      <td>52</td>
      <td>43</td>
      <td>60</td>
      <td>50</td>
      <td>65</td>
      <td>1</td>
      <td>False</td>
    </tr>
    <tr>
      <th>5</th>
      <td>Charmeleon</td>
      <td>Fire</td>
      <td>NaN</td>
      <td>405</td>
      <td>58</td>
      <td>64</td>
      <td>58</td>
      <td>80</td>
      <td>65</td>
      <td>80</td>
      <td>2</td>
      <td>False</td>
    </tr>
  </tbody>
</table>
</div>



## Seaborn 的画图方法

Seaborn 最大的优势之一是其多样的绘图功能。例如，制作散点图只需要一句 `lmplot()` 即可.

有两种方法可以做到这一点:

* 第一种方法（推荐）是将你的 `DataFrame` 传递给 `data =`，同时将列名传递给轴参数，`x =` 和 `y =`。
* 第二种方法是直接将一系列数据传递给轴参数。

例如，我们来比较我们的"神奇宝贝"的攻击和防御统计：


```python
# Recommended way
sns.lmplot(x='Attack', y='Defense', data=df)
 
# Alternative way
# sns.lmplot(x=df.Attack, y=df.Defense)
```




    <seaborn.axisgrid.FacetGrid at 0x11044b4e0>




![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-02-28-Seaborn-basics/output_6_1.png)


Seaborn 没有专门的散点图函数，这就是为什么你看到一条对角线。我们实际上使用了 Seaborn 拟合和绘制**回归线**的函数。

不过，每个绘图方法都有几个有用的选项。我们可以设置一下 `lmplot()`：

* 首先，设置 `fit_reg = False` 来移除回归线，因为我们只需要一个散点图。
* 然后，设置 `hue ='Stage'` 根据神奇宝贝的进化阶段着色。这个 `hue` 参数非常有用，因为它允许你使用颜色表示第三维信息。


```python
# Scatterplot arguments
sns.lmplot(x='Attack', y='Defense', data=df,
           fit_reg=False, # No regression line
           hue='Stage')   # Color by evolution stage
```




    <seaborn.axisgrid.FacetGrid at 0x1105b6a58>




![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-02-28-Seaborn-basics/output_8_1.png)


漂亮多了吧? 如果我们想改变坐标轴范围呢?

## 使用 Matplotlib 做自定义


记住，Seaborn 是 Matplotlib 的高级接口。Seaborn 将会为您提供大部分解决方法，但有时你也需要 Matplotlib 的帮助。

设置坐标轴范围就是其中一个例子：

* 首先，照常调用你的 Seaborn 绘图方法。
* 然后，调用 Matplotlib 的自定义函数。在这个例子中，我们将使用它 `ylim()`和` xlim()`。


```python
# Plot using Seaborn
sns.lmplot(x='Attack', y='Defense', data=df,
           fit_reg=False, 
           hue='Stage')
 
# Tweak using Matplotlib
plt.ylim(-10, None)
plt.xlim(-10, None)
```




    (-10, 141.9881096001028)




![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-02-28-Seaborn-basics/output_11_1.png)


## Pandas 的作用


尽管这是 Seaborn 教程，但 Pandas 实际上扮演着非常重要的角色。如果能够合理使用 `DataFrame`, Seaborn 会更加好用。

假设为神奇宝贝的战斗统计量绘制 box plot


```python
# Boxplot
sns.boxplot(data=df)
```




    <matplotlib.axes._subplots.AxesSubplot at 0x1119424e0>




![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-02-28-Seaborn-basics/output_13_1.png)


这是个说得过去的开始，但是我们可能希望删除一些列：

* 我们可以删除 Total，因为数据有单独统计。
* 我们可以移除 Stage 和 Legendary，因为它们不是战斗统计。

不过 Seaborn 来做这些可不容易。相反，DataFrame 做起来很简单。

让我们创建一个名为 `stats_df` 的新 DataFrame，它只保留统计信息列:


```python
# Pre-format DataFrame
stats_df = df.drop(['Total', 'Stage', 'Legendary'], axis=1)
 
# New boxplot using stats_df
sns.boxplot(data=stats_df)
```




    <matplotlib.axes._subplots.AxesSubplot at 0x111ab70b8>




![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-02-28-Seaborn-basics/output_15_1.png)


## Seaborn 主题风格


Seaborn 的另一个优点是它带来了优雅的风格主题。

接下来，制作 violin plot 并将主题更改为 “darkgrid”。

* violin plot 是 box plot 的良好替代品。
* 它能显示分布（通过小提琴的宽度），而不是仅汇总统计。

例如，我们可以看到神奇宝贝主要类型的攻击分布：


```python
# Set theme
sns.set_style('darkgrid')
 
# Violin plot
sns.violinplot(x='Type 1', y='Attack', data=df)
```




    <matplotlib.axes._subplots.AxesSubplot at 0x111dc2898>




![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-02-28-Seaborn-basics/output_17_1.png)


正如你所看到的，Dragon 比 Ghost 更具攻击性，但它们也有更大的差异。

## 更多

### Heatmap

查看矩阵数据


```python
# Calculate correlations
corr = stats_df.corr()
 
# Heatmap
sns.heatmap(corr)
```




    <matplotlib.axes._subplots.AxesSubplot at 0x111fd6f98>




![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-02-28-Seaborn-basics/output_20_1.png)


### Histogram

查看离散数据分布


```python
# Distribution Plot (a.k.a. Histogram)
sns.distplot(df.Attack)
```




    <matplotlib.axes._subplots.AxesSubplot at 0x19763277be0>




![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-02-28-Seaborn-basics/output_22_1.png)


### Bar Plot

查看范畴数据分布


```python
# Count Plot (a.k.a. Bar Plot)
sns.countplot(x='Type 1', data=df, palette=pkmn_type_colors)
 
# Rotate x-labels
plt.xticks(rotation=-45)
```




    (array([ 0,  1,  2,  3,  4,  5,  6,  7,  8,  9, 10, 11, 12, 13, 14]),
     <a list of 15 Text xticklabel objects>)




![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-02-28-Seaborn-basics/output_24_1.png)


### Factor Plot

根据种类分别绘图


```python
# Factor Plot
g = sns.factorplot(x='Type 1', 
                   y='Attack', 
                   data=df, 
                   hue='Stage',  # Color by stage
                   col='Stage',  # Separate by stage
                   kind='swarm') # Swarmplot
 
# Rotate x-axis labels
g.set_xticklabels(rotation=-45)
 
# Doesn't work because only rotates last plot
# plt.xticks(rotation=-45)
```




    <seaborn.axisgrid.FacetGrid at 0x1976332a5c0>




![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-02-28-Seaborn-basics/output_26_1.png)


### Joint Distribution Plot

联合分布, 查看二维数据


```python
# Joint Distribution Plot
sns.jointplot(x='Attack', y='Defense', data=df)
```




    <seaborn.axisgrid.JointGrid at 0x197633df240>




![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-02-28-Seaborn-basics/output_28_1.png)


现在你已经了解了 Seaborn 基础, 下一步可以边做边学了.
