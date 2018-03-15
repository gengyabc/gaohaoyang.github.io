---
layout: post
title:  "科学计算13 - Seaborn 高级"
categories: 科学计算
tags:  Seaborn
author: Geng
---

* content
{:toc}

有了前面 `seaborn` 的基础, 这里对它进行一些深入介绍, 并分析在什么时候应该使用什么图.

seaborn 本身建立在 Matplotlib 之上, 它自己也有低级方法和高级方法. 低级方法直接使用 Matplotlib 操作数值数据, 范畴数据和回归. 高级方法调用低级方法绘图, 可以更快速的画图. 如果想要更多的画图自主性, 那么还是要用低级方法. 当然, 最高的自主性就是直接使用 Matplotlib, 但是比较费事.






这个层级关系可以参考下图

![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/seaborn.png)

结合上面层级关系, 看下面的脑图会有一个更全面的映像:

![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/mindmap.png)

## Visualizing the distribution of a dataset

这里介绍绘制数值数据分布图的方法:

1. 单变量: `displot` 绘制直方图和概率密度曲线
2. 双变量: `jointplot` 绘制联合概率
3. 成对关系: `pairplot` 绘制数据的成对关系 


```python
import numpy as np
import seaborn as sns
import pandas as pd
import matplotlib.pyplot as plt
%matplotlib inline
```


```python
np.random.seed(sum(map(ord, "distributions")))
sns.set(color_codes=True)
```

### 绘制单变量分布 Plotting univariate distributions


快速查看 seaborn 中单变量分布的最便捷方法是 `distplot()` 函数。默认情况下，绘制直方图并拟合核密度估计值（kernel density estimate, KDE).


```python
x = np.random.normal(size=100)
sns.distplot(x);
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_5_0.png)


#### 直方图 Histograms


让我们删除密度曲线并添加一个 rug plot (地毯图)，这个图对每次观测值绘制一个小的垂直刻度。你可以用 `rugplot()` 函数直接绘制地毯图，但是它也可以在 `distplot()` 中使用：


```python
sns.distplot(x, kde=False, rug=True);
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_7_0.png)


#### Kernel density estimaton (核密度估计, KDE)


大家可能不熟悉核密度估计，但它可能是绘制分布形状的有用工具。就像直方图一样，KDE 图将一个轴上的观测密度与另一个轴上的高度进行编码：


```python
sns.distplot(x, hist=False, rug=True);
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_9_0.png)


### 绘制二元变量分布 Plotting bivariate distributions


可视化两个变量的二元分布也很有用。在 seaborn 中最简单的方法是使用 `jointplot()` 函数，该函数创建一个多面板图形，在每个轴上显示两个变量之间的二元（或联合）关系以及单变量（或边际）分布。


```python
mean, cov = [0, 1], [(1, .5), (.5, 1)]
data = np.random.multivariate_normal(mean, cov, 200)
df = pd.DataFrame(data, columns=["x", "y"])

sns.jointplot(x="x", y="y", data=df)
```




    <seaborn.axisgrid.JointGrid at 0x1d1dd4439b0>




![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_11_1.png)


### 可视化数据集中的成对关系 Visualizing pairwise relationships in a dataset

要在数据集中绘制多个成对的双变量分布，可以使用 `pairplot()` 函数。这将创建一个矩阵并显示 DataFrame 中各个列的成对关系。默认情况下，它也绘制每个变量在对角轴上的单变量分布


```python
iris = sns.load_dataset("iris")
# 如果数据不能载入， 下载数据然后读入: https://github.com/mwaskom/seaborn-data
# iris = pd.read_csv('data/iris.csv')
sns.pairplot(iris)
```




    <seaborn.axisgrid.PairGrid at 0x104180cc0>




![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_13_1.png)


## 绘制范畴数据(分类数据)Plotting with categorical data

这部分介绍如何绘制范畴数据

可以将 seaborn 的范畴图分为三类

* 显示分类变量的每个级别的每个观察结果：`swarmplot（）`和`stripplot（）`
* 显示每个观测分布的抽象表示：`boxplot（）`和`violinplot（）`
* 应用统计估计来显示集中趋势和置信区间的度量：`barplot（）`和`pointplot（）`

尽管每个 API 都有特定的参数来控制应用于该数据的可视化的细节, 不过这些函数共享一个基本的 API 来接收数据.

上面提到的函数都是低级函数，因为它们绘制在特定的 matplotlib 轴上。

还有更高级别的 `factorplot()`，它将这些函数与 `FacetGrid` 结合起来，在图形面板的**网格上绘制分类图**。


```python
sns.set(style="whitegrid", color_codes=True)
np.random.seed(sum(map(ord, "categorical")))
# titanic = pd.read_csv('data/titanic.csv')
# tips = pd.read_csv('data/tips.csv')
# iris = pd.read_csv('data/iris.csv')
titanic = sns.load_dataset("titanic")
tips = sns.load_dataset("tips")
iris = sns.load_dataset("iris")
```

### 散点图 Categorical scatterplots


显示某个变量的值在一个级别上的值可以使用 `stripplot()`，它将散点图推广到其中一个变量是范畴数据的情况：


```python
sns.stripplot(x="day", y="total_bill", data=tips);
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_18_0.png)


在条状图中，散点图通常会重叠。这使得很难看到数据的完整分布。一个简单的解决方案是使用一些随机的“抖动(jitter)”来调整位置（仅沿分类轴）：


```python
sns.stripplot(x="day", y="total_bill", data=tips, jitter=True);
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_20_0.png)


一种不同的方法是使用函数 `swarmplot()`，它使用点避免重叠的算法将每个散点图分布在分类轴上：


```python
sns.swarmplot(x="day", y="total_bill", data=tips);
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_22_0.png)


也可以用 `hue` 参数添加一个嵌套的分类变量：


```python
sns.swarmplot(x="day", y="total_bill", hue="sex", data=tips);
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_24_0.png)


有时候，将分类变量放在垂直轴上可能更好（当类别名称相对较长或有很多类别时，这是非常有用的）。可以使用 `orient` 关键字设定一个方向，但通常可以通过传递给 `x` 和 `y` 变量的数据类型来推断绘图方向：


```python
sns.swarmplot(x="total_bill", y="day", hue="time", data=tips);
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_26_0.png)


### 分类内数据的分布 Distributions of observations within categories

有时候，散点图提供的信息很有限。有几种方法可以通过各种方式对这些信息进行总结，以便在整个类别级别进行比较。

#### Boxplots


```python
sns.boxplot(x="day", y="total_bill", hue="time", data=tips);
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_29_0.png)



```python
sns.boxplot(x="day", y="total_bill", data=tips);
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_30_0.png)


#### Violinplots

另一种方法是`violinplot()`，它将 boxplot 与核密度估计相结合：


```python
sns.violinplot(x="total_bill", y="day", hue="time", data=tips);
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_33_0.png)


当 `hue` 参数只有两个等级时，也可以“拆分”小提琴，这样可以更有效地利用空间：


```python
sns.violinplot(x="day", y="total_bill", hue="sex", data=tips, split=True);
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_35_0.png)


### 统计估计 Statistical estimation within categories


很多时候，想要展示这些数值的集中趋势，而不是显示每个类别中的分布情况。 Seaborn 主要有两种显示这些信息的方式

#### Bar plots



```python
sns.barplot(x="sex", y="survived", hue="class", data=titanic);
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_38_0.png)


当你想显示每个类别的观察数量而不是比例时, 可以使用 `countplot()`：


```python
sns.countplot(x="deck", data=titanic, palette="Greens_d");
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_40_0.png)



```python
sns.countplot(y="deck", hue="class", data=titanic);
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_41_0.png)


#### Point plots


`pointplot()` 函数提供了查看相同信息的另一种方式。此函数也会将 `y` 轴上的高度值编码，但不是显示完整的柱形图，而是绘制点估计值和置信区间。另外，`pointplot` 连接相同“色调(hue)”的点。这可以很容易地看出 `y` 随 `x` 的变化.


```python
sns.pointplot(x="sex", y="survived", hue="class", data=titanic);
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_43_0.png)


## 绘制多面板图Drawing multi-panel categorical plots


正如我们上面提到的，有两种方法可以绘制 `seaborn` 分类图。可以使用上面介绍的函数，或者使用将这些函数与 `FacetGrid()` 结合起来的高级函数 `factorplot()`。默认情况下，`factorplot()` 生成一个 `pointplot()`：

### factorplot and FacetGrid

`seaborn` 的一大能力就是很容易画条件图


```python
sns.factorplot(x="day", y="total_bill", hue="smoker", data=tips);
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_47_0.png)


`kind` 参数可以让你选择上面讨论的任何一种图：


```python
sns.factorplot(x="day", y="total_bill", hue="smoker", data=tips, kind="bar");
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_49_0.png)


使用 `factorplot()` 的主要优点是可以很容易地绘图并调查其他分类变量的作用：


```python
sns.factorplot(x="day", y="total_bill", hue="smoker",
               col="time", data=tips, kind="swarm");  # 根据时间不同, 分布在不同列
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_51_0.png)



```python
sns.factorplot(x="day", y="total_bill", hue="smoker",
               row="time", data=tips, kind="swarm");  # 根据时间不同, 分布在不同行
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_52_0.png)


我们可以绘制任何类型的图。由于 `FacetGrid` 的工作方式，要改变图形的大小和形状，需要指定每个子图的 `size` 和 `aspect` 参数：


```python
sns.factorplot(x="time", y="total_bill", hue="smoker",
               col="day", data=tips, kind="box", size=4, aspect=.5)
```




    <seaborn.axisgrid.FacetGrid at 0x2df10f14dd8>




![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_54_1.png)


`factorplot` 一大好处是可以一次完成所有工作，而不必自己分割数据并单独创建条件图。

`FacetGrid` 对象稍微复杂一点，但也更强大，采取同样的想法。假设我们想看KDE图：


```python
g = sns.FacetGrid(data=tips, col="day")  # 建立 FacetGrid 对象, 以 day 为分类画在几个列上
g.map(sns.distplot, "total_bill")  # 使用 FacetGrid 对象方法 map(绘图方法, 数据) 绘图
```




    <seaborn.axisgrid.FacetGrid at 0x2df10de6390>




![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_56_1.png)



```python
titanic.head()
```




<div>
<style>
    .dataframe thead tr:only-child th {
        text-align: right;
    }

    .dataframe thead th {
        text-align: left;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>survived</th>
      <th>pclass</th>
      <th>sex</th>
      <th>age</th>
      <th>sibsp</th>
      <th>parch</th>
      <th>fare</th>
      <th>embarked</th>
      <th>class</th>
      <th>who</th>
      <th>adult_male</th>
      <th>deck</th>
      <th>embark_town</th>
      <th>alive</th>
      <th>alone</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>3</td>
      <td>male</td>
      <td>22.0</td>
      <td>1</td>
      <td>0</td>
      <td>7.2500</td>
      <td>S</td>
      <td>Third</td>
      <td>man</td>
      <td>True</td>
      <td>NaN</td>
      <td>Southampton</td>
      <td>no</td>
      <td>False</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>1</td>
      <td>female</td>
      <td>38.0</td>
      <td>1</td>
      <td>0</td>
      <td>71.2833</td>
      <td>C</td>
      <td>First</td>
      <td>woman</td>
      <td>False</td>
      <td>C</td>
      <td>Cherbourg</td>
      <td>yes</td>
      <td>False</td>
    </tr>
    <tr>
      <th>2</th>
      <td>1</td>
      <td>3</td>
      <td>female</td>
      <td>26.0</td>
      <td>0</td>
      <td>0</td>
      <td>7.9250</td>
      <td>S</td>
      <td>Third</td>
      <td>woman</td>
      <td>False</td>
      <td>NaN</td>
      <td>Southampton</td>
      <td>yes</td>
      <td>True</td>
    </tr>
    <tr>
      <th>3</th>
      <td>1</td>
      <td>1</td>
      <td>female</td>
      <td>35.0</td>
      <td>1</td>
      <td>0</td>
      <td>53.1000</td>
      <td>S</td>
      <td>First</td>
      <td>woman</td>
      <td>False</td>
      <td>C</td>
      <td>Southampton</td>
      <td>yes</td>
      <td>False</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0</td>
      <td>3</td>
      <td>male</td>
      <td>35.0</td>
      <td>0</td>
      <td>0</td>
      <td>8.0500</td>
      <td>S</td>
      <td>Third</td>
      <td>man</td>
      <td>True</td>
      <td>NaN</td>
      <td>Southampton</td>
      <td>no</td>
      <td>True</td>
    </tr>
  </tbody>
</table>
</div>




```python
g = sns.FacetGrid(data=titanic, col="sex") 
g.map(plt.scatter, "age", "fare")
```




    <seaborn.axisgrid.FacetGrid at 0x2df11076828>




![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_58_1.png)


`FacetGrid` 让我们可以将任何绘图功能映射到数据的每个部分。例如，上面我们将 `plt.scatter` 赋予 `g.map`，它告诉 Seaborn 将 matplotlib `plt.scatter` 函数应用于每段数据。我们可以使用任何理解输入数据的函数。例如，我们可以绘制回归图：


```python
g = sns.FacetGrid(titanic, col="sex") 
g.map(sns.regplot, "age", "fare") 
```




    <seaborn.axisgrid.FacetGrid at 0x2df110feb00>




![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_60_1.png)


我们甚至可以一次分割多个变量，一些沿着行分布, 一些沿着列分布。这对于比较条件分布非常有用：


```python
g = sns.FacetGrid(titanic, col="sex", row="survived") 
g.map(sns.kdeplot, "age", "fare") 
```




    <seaborn.axisgrid.FacetGrid at 0x1094eeda0>




![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_62_1.png)


### pairplot and PairGrid

`PairGrid` 可以帮助显示**数个变量的分类关系**, 用法和 `FacetGrid` 类似：


```python
g = sns.PairGrid(tips,
                 x_vars=["smoker", "time", "sex"],
                 y_vars=["total_bill", "tip"],
                 aspect=.75, size=3.5)
g.map(sns.violinplot, palette="pastel");
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_64_0.png)



```python
g = sns.pairplot(tips,
                 x_vars=["smoker", "time", "sex"],
                 y_vars=["total_bill", "tip"],
                 aspect=.75, size=3.5)
g.map(sns.violinplot, palette="pastel");
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_65_0.png)


## 观察线性关系 Visualizing linear relationships


许多数据集包含多个变量，分析的目标通常是分析这些变量相互关系。我们先前讨论了通过显示两个变量的联合分布来实现这个功能。不过，使用统计模型来估计两组有噪声的观测之间的简单关系很有用。通过线性回归可以方便的完成这个功能。


```python
sns.set(color_codes=True)
np.random.seed(sum(map(ord, "regression")))
```

### 绘制线性回归模型的方法 Functions to draw linear regression models

seaborn 主要有两个方法通过回归显示线性关系。这两个方法一个是 `regplot`, 一个是 `lmplot`, 而且密切相关。

这两个函数都绘制两个变量 `x` 和 `y` 的散点图，然后拟合回归模型 `y〜x`, 并绘制回归线和该回归的 95％ 置信区间：


```python
sns.regplot(x="total_bill", y="tip", data=tips);
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_69_0.png)



```python
sns.lmplot(x="total_bill", y="tip", data=tips)
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_70_0.png)


应该注意，除了图形的形状不同之外，所得到的图是相同的。我们将解释其原因

目前，另一个主要区别是，`regplot()` 接受各种形式的 x 和 y 变量，包括简单的 `numpy` 数组，`pandas` `Series` 对象，或者 `pandas` `DataFrame` 对象中变量的引用。相反，`lmplot()` 将数据作为必需的参数，并且必须将 x 和 y 变量指定为字符串。这种数据格式被称为“长形”或“整洁”数据。除了这种输入灵活性外，`regplot()` 的特性是 `lmplot()` 的子集，所以我们将使用后者来演示它们。

当其中一个变量采用离散值时，可以拟合线性回归，但是，由这类数据集产生的简单散点图往往不是最优的：


```python
sns.lmplot(x="size", y="tip", data=tips)
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_72_0.png)


可以向离散值添加一些随机噪声（“抖动(jitter)”），以使这些值的分布更清晰。请注意，抖动仅应用于散点图数据，不会影响回归线拟合本身：


```python
sns.lmplot(x="size", y="tip", data=tips, x_jitter=.05)
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_74_0.png)


或者折叠每个离散集中的观察值以绘制集中趋势的估计以及置信区间：


```python
sns.lmplot(x="size", y="tip", data=tips, x_estimator=np.mean)
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_76_0.png)


### 拟合不同种类的模型 Fitting different kinds of models

上面使用的简单线性回归模型非常简单，但是，它不适用于某些类型的数据集。 Anscombe 数据集显示了几个例子，其中简单线性回归提供了相同的估计, 但是一看就知道它们是不同的。下面例子中，第一个例子用线性回归模型很好：


```python
anscombe = sns.load_dataset("anscombe")
```


```python
sns.lmplot(x="x", y="y", data=anscombe[anscombe['dataset']=='I'], ci=None, scatter_kws={"s": 80})
```




    <seaborn.axisgrid.FacetGrid at 0x109489828>




![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_79_1.png)


第二个数据集的线性模型相同，但一看就知道这个模型不好：


```python
sns.lmplot(x="x", y="y", data=anscombe[anscombe['dataset']=='II'], ci=None, scatter_kws={"s": 80})
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_81_0.png)


存在这些高阶关系的话，`lmplot()` 和 `regplot()` 可以拟合一个多项式回归模型, 用它来探索数据集的简单的非线性趋势：


```python
sns.lmplot(x="x", y="y", data=anscombe[anscombe['dataset']=='II'], order=2, ci=None, scatter_kws={"s": 80})
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_83_0.png)



```python
sns.lmplot(x="x", y="y", data=anscombe.query("dataset == 'I'"),
           ci=None, scatter_kws={"s": 80})
```




    <seaborn.axisgrid.FacetGrid at 0x2df12991940>




![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_84_1.png)


除了主要关系，由于某种原因而偏离的“异常值”也会有问题：


```python
sns.lmplot(x="x", y="y", data=anscombe[anscombe['dataset']=='III'], ci=None, scatter_kws={"s": 80})
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_86_0.png)


如果有异常值，拟合稳健回归可能很有用，该回归使用不同的损失函数来减轻相对较大的残差：


```python
sns.lmplot(x="x", y="y", data=anscombe[anscombe['dataset']=='III'], robust=True, ci=None, scatter_kws={"s": 80})
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_88_0.png)


当 `y` 是二元变量时，简单线性回归好像也 OK，但是结果你能信吗?


```python
tips["big_tip"] = (tips.tip / tips.total_bill) > .15
sns.lmplot(x="total_bill", y="big_tip", data=tips,
           y_jitter=.03)
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_90_0.png)


在这种情况下的解决方案是拟合逻辑回归，使得回归线显示对于给定值 `x`, 估计 `y = 1` 的概率：


```python
sns.lmplot(x="total_bill", y="big_tip", data=tips,
           logistic=True, y_jitter=.03)
```




    <seaborn.axisgrid.FacetGrid at 0x2df144760f0>




![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_92_1.png)


> 逻辑回归估计的计算强度要高于简单回归，并且由于回归线周围的置信区间是使用引导程序计算得出的，因此你可能希望关闭它以获得更快迭代（使用 `ci = None`）

`residplot()` 函数可以检查简单回归模型是否适合数据集。它拟合并删除一个简单的线性回归，然后绘制每个观察值的残差值。理想情况下，这些值应该在 `y = 0` 周围随机分布：


```python
sns.residplot(x="x", y="y", data=anscombe.query("dataset == 'I'"),
              scatter_kws={"s": 80});
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_95_0.png)



```python
sns.residplot(x="x", y="y", data=anscombe[anscombe['dataset'] == 'I'], scatter_kws={"s": 80})
```




    <matplotlib.axes._subplots.AxesSubplot at 0x2df14469390>




![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_96_1.png)


如果残差中存在什么规律，则表明简单线性回归不合适：


```python
sns.residplot(x="x", y="y", data=anscombe[anscombe['dataset'] == 'II'], scatter_kws={"s": 80})
```




    <matplotlib.axes._subplots.AxesSubplot at 0x2df12db3ac8>




![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_98_1.png)


### 条件分布 Conditioning on other variables

以上展示了很多探索两两关系的方法。然而，更常见的问题是“这两个变量之间的关系如何随着第三个变量的变化而变化？” **这就是 `regplot()` 和 `lmplot()` 之间出现差异的地方**。`regplot()` 总是显示单一的关系，而 `lmplot()` 将 `regplot()` 与 `FacetGrid` 结合起来，提供了一个简单的界面，在“分面”图上显示线性回归.

分离出关系的最好方法是在同一轴上绘制两个种类，并使用颜色区分它们：


```python
sns.lmplot(x="total_bill", y="tip", hue="smoker", data=tips)
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_100_0.png)


要添加另一个变量，可以绘制多个“子面”，其中每个变量出现在不同行或列中：


```python
sns.lmplot(x="total_bill", y="tip", hue="smoker", col="time", data=tips)
```




    <seaborn.axisgrid.FacetGrid at 0x2df12c42400>




![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_102_1.png)



```python
sns.lmplot(x="total_bill", y="tip", hue="smoker", col="time", row="sex", data=tips)
```




    <seaborn.axisgrid.FacetGrid at 0x2df144cc0f0>




![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_103_1.png)


### 其他回归线场景 Plotting a regression in other contexts

更复杂的情况中, 可以使用 `regplot()` 作为底层工具。比如 `jointplot()`函数可以使用 `regplot()` 绘图. 通过传递 `kind =“reg”` 来显示线性回归拟合：


```python
sns.jointplot(x="total_bill", y="tip", data=tips, kind="reg")
```




    <seaborn.axisgrid.JointGrid at 0x2df1582f630>




![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_105_1.png)


`pairplot()` 使用 `kind =“reg”` 作为参数, 可以结合 `regplot()` 和 `PairGrid` 来显示数据集线性关系。

> 注意，这与 `lmplot()` 不同。在下图中，没有显示基于其他数据的条件关系, 而是显示数据集中变量两辆之间的个关系


```python
sns.pairplot(tips, x_vars=["total_bill", "size"], y_vars=["tip"], size=5, aspect=.8, kind="reg")
```




    <seaborn.axisgrid.PairGrid at 0x2df15efafd0>




![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_107_1.png)


可以使用 `hue` 参数显示条件分布


```python
sns.pairplot(tips, x_vars=["total_bill", "size"], y_vars=["tip"], hue="smoker", size=5, aspect=.8, kind="reg")
```




    <seaborn.axisgrid.PairGrid at 0x2df15f2a7f0>




![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_109_1.png)


## 绘制数据感知网格 Plotting on data-aware grids

在探索中等维数据时，一种有用的方法是数据集的不同子集绘制同一图的多个实例。这种技术有时被称为“格子”绘图。它使人能够快速提取大量有关的复杂信息。 Matplotlib 为制作多轴图提供了很好的支持;seaborn 建立在此之上，直接将绘图结构与数据集结构联系起来。

数据必须位于 `Pandas` `DataFrame`，并且必须采用 Hadley Whickam 所谓的“整齐”数据的形式。简而言之，这意味着您的数据框架应该结构化，以便每列都是一个变量，每一行都是一个观察值。

> 后面高级内容, 不翻译了

> For advanced use, you can use the objects discussed in this part of the tutorial directly, which will provide maximum flexibility. Some seaborn functions (such as `lmplot()`, `factorplot()`, and `pairplot()`) also use them behind the scenes. Unlike other seaborn functions that are “Axes-level” and draw onto specific (possibly already-existing) matplotlib Axes without otherwise manipulating the figure, these higher-level functions create a figure when called and are generally more strict about how it gets set up. In some cases, arguments either to those functions or to the constructor of the class they rely on will provide a different interface attributes like the figure size, as in the case of `lmplot()` where you can set the height and aspect ratio for each facet rather than the overall size of the figure. Any function that uses one of these objects will always return it after plotting, though, and most of these objects have convenience methods for changing how the plot is drawn, often in a more abstract and easy way.



```python
sns.set(style="ticks")
np.random.seed(sum(map(ord, "axis_grids")))
```

The `FacetGrid` is an object that links a Pandas DataFrame to a matplotlib figure with a particular structure.

The `FacetGrid` class is useful when you want to visualize the distribution of a variable or the relationship between multiple variables separately within subsets of your dataset. A `FacetGrid` can be drawn with up to three dimensions: `row`, `col`, and `hue`. The first two have obvious correspondence with the resulting array of axes; think of the `hue` variable as a third dimension along a depth axis, where different levels are plotted with different colors.

The class is used by initializing a `FacetGrid` object with a dataframe and the names of the variables that will form the `row`, `column`, or `hue` dimensions of the grid. These variables should be categorical or discrete, and then the data at each level of the variable will be used for a facet along that axis. For example, say we wanted to examine differences between lunch and dinner in the tips dataset.

Additionally, both `lmplot()` and `factorplot()` use this object internally, and they return the object when they are finsihed so that it can be used for further tweaking.


```python
tips = sns.load_dataset("tips")
g = sns.FacetGrid(tips, col="time")
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_113_0.png)


The main approach for visualizing data on this grid is with the `FacetGrid.map()` method. Provide it with a plotting function and the name(s) of variable(s) in the dataframe to plot. Let’s look at the distribution of tips in each of these subsets, using a histogram.


```python
g = sns.FacetGrid(tips, col="time")
g.map(plt.hist, "tip")
```




    <seaborn.axisgrid.FacetGrid at 0x2df10fc1860>




![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_115_1.png)



```python
g = sns.FacetGrid(tips, col="sex", hue="smoker")
g.map(plt.scatter, "total_bill", "tip", alpha=.7)
g.add_legend()
```




    <seaborn.axisgrid.FacetGrid at 0x2df10efa8d0>




![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_116_1.png)


The default ordering of the facets is derived from the information in the DataFrame. If the variable used to define facets has a categorical type, then the order of the categories is used. Otherwise, the facets will be in the order of appearence of the category levels. It is possible, however, to specify an ordering of any facet dimension with the appropriate `*_order` parameter:


```python
ordered_days = tips.day.value_counts().index
g = sns.FacetGrid(tips, row="day", row_order=ordered_days, size=1.7, aspect=4,)
g.map(sns.distplot, "total_bill", hist=False, rug=True)
```




    <seaborn.axisgrid.FacetGrid at 0x2df1612c668>




![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_118_1.png)


### Plotting pairwise relationships in a dataset

`PairGrid` also allows you to quickly draw a grid of small subplots using the same plot type to visualize data in each. In a `PairGrid`, each row and column is assigned to a different variable, so the resulting plot shows each pairwise relationship in the dataset. This style of plot is sometimes called a “scatterplot matrix”, as this is the most common way to show each relationship, but `PairGrid` is not limited to scatterplots.

It’s important to understand the differences between a `FacetGrid` and a `PairGrid`. In the former, each facet shows the same relationship conditioned on different levels of other variables. In the latter, each plot shows a different relationship (although the upper and lower triangles will have mirrored plots). Using `PairGrid` can give you a very quick, very high-level summary of interesting relationships in your dataset.

The basic usage of the class is very similar to `FacetGrid`. First you initialize the grid, then you pass plotting function to a map method and it will be called on each subplot. There is also a companion function, `pairplot()` that trades off some flexibility for faster plotting.


```python
g = sns.PairGrid(iris)
g.map(plt.scatter)
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_120_0.png)


It’s possible to plot a different function on the diagonal to show the univariate distribution of the variable in each column. Note that the axis ticks won’t correspond to the count or density axis of this plot, though.


```python
g = sns.PairGrid(iris)
g.map_diag(plt.hist)
g.map_offdiag(plt.scatter)
```




    <seaborn.axisgrid.PairGrid at 0x2df17a9d8d0>




![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_122_1.png)


A very common way to use this plot colors the observations by a separate categorical variable. For example, the iris dataset has four measurements for each of three different species of iris flowers so you can see how they differ.


```python
g = sns.PairGrid(iris, hue="species")
g.map_diag(plt.hist)
g.map_offdiag(plt.scatter)
g.add_legend()
```




    <seaborn.axisgrid.PairGrid at 0x2df1811d160>




![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_124_1.png)


By default every numeric column in the dataset is used, but you can focus on particular relationships if you want.


```python
g = sns.PairGrid(iris, vars=["sepal_length", "sepal_width"], hue="species")
g.map(plt.scatter)
```




    <seaborn.axisgrid.PairGrid at 0x2df1860d240>




![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_126_1.png)


It’s also possible to use a different function in the upper and lower triangles to emphasize different aspects of the relationship.


```python
g = sns.PairGrid(iris)
g.map_upper(plt.scatter)
g.map_lower(sns.kdeplot, cmap="Blues_d")
g.map_diag(sns.kdeplot, lw=3, legend=False);
```

    C:\Users\h\Anaconda2\envs\py36\lib\site-packages\matplotlib\contour.py:967: UserWarning: The following kwargs were not used by contour: 'label', 'color'
      s)



![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_128_1.png)



```python
g = sns.PairGrid(tips, y_vars=["tip"], x_vars=["total_bill", "size"], size=4)
g.map(sns.regplot)
g.set(ylim=(-1, 11), yticks=[0, 5, 10])
```




    <seaborn.axisgrid.PairGrid at 0x2df1917c320>




![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_129_1.png)


`PairGrid` is flexible, but to take a quick look at a dataset, it can be easier to use `pairplot()`. This function uses scatterplots and histograms by default, although a few other kinds will be added (currently, you can also plot regression plots on the off-diagonals and KDEs on the diagonal).


```python
sns.pairplot(iris, hue="species", size=2.5)
```




    <seaborn.axisgrid.PairGrid at 0x2df1917c908>




![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_131_1.png)


You can also control the aesthetics of the plot with keyword arguments, and it returns the PairGrid instance for further tweaking.


```python
g = sns.pairplot(iris, hue="species", palette="Set2", diag_kind="kde", size=2.5)
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_133_0.png)


## 设置绘图显示效果 Controlling figure aesthetics

Matplotlib is highly customizable, but it can be hard to know what settings to tweak to achieve an attractive plot. Seaborn comes with a number of customized themes and a high-level interface for controlling the look of matplotlib figures.


```python
import numpy as np
import matplotlib as mpl
import matplotlib.pyplot as plt
import seaborn as sns
import pandas as pd
%matplotlib inline
np.random.seed(sum(map(ord, "aesthetics")))
```

Let’s define a simple function to plot some offset sine waves, which will help us see the different stylistic parameters we can tweak.


```python
def sinplot(flip=1):
    x = np.linspace(0, 14, 100)
    for i in range(1, 7):
        plt.plot(x, np.sin(x + i * .5) * (7 - i) * flip)
        
sinplot()
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_137_0.png)



```python
# To switch to seaborn defaults, simply call the set() function.
sns.set()
sinplot()
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_138_0.png)


Seaborn splits matplotlib parameters into two independent groups. The first group sets the aesthetic style of the plot, and the second scales various elements of the figure so that it can be easily incorporated into different contexts.

The interface for manipulating these parameters are two pairs of functions. To control the style, use the axes_style() and set_style() functions. To scale the plot, use the plotting_context() and set_context() functions. In both cases, the first function returns a dictionary of parameters and the second sets the matplotlib defaults.

### Seaborn figure styles

There are five preset seaborn themes: darkgrid, whitegrid, dark, white, and ticks. They are each suited to different applications and personal preferences. The default theme is darkgrid. As mentioned above, the grid helps the plot serve as a lookup table for quantitative information, and the white-on grey helps to keep the grid from competing with lines that represent data. The whitegrid theme is similar, but it is better suited to plots with heavy data elements:


```python
sns.set_style("whitegrid")
data = np.random.normal(size=(20, 6)) + np.arange(6) / 2
sns.boxplot(data=data);
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_141_0.png)


### Removing axes spines

Both the white and ticks styles can benefit from removing the top and right axes spines, which are not needed. It’s impossible to do this through the matplotlib parameters, but you can call the seaborn function despine() to remove them:


```python
sns.set_style("ticks")
sns.set_context("poster")
sinplot()
sns.despine()
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_143_0.png)


## Choosing color palettes


```python
current_palette = sns.color_palette()
sns.palplot(current_palette)
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2018-03-15-Seaborn-advanced/output_145_0.png)

