---
layout: post
title:  "科学计算7 - Python在统计学的应用"
categories: 科学计算
tags:  Pandas Statsmodels 
author: Geng
---

* content
{:toc}


这部分内容主要来自[此教程](http://www.scipy-lectures.org/packages/statistics/index.html)

需要下列依赖：
* 基本科学计算包 (numpy, scipy, matplotlib)
* Pandas
* Statsmodels






## 数据展示和交互
下面例子用到此[资源文件](http://www.scipy-lectures.org/_downloads/brain_size.csv)，请先下载。
我们使用**pandas.DataFrame**来存取操作数据，它是Python版的电子表格。

> pandas是一个开源Python库，提供高效易用的数据结构和数据分析工具。

### 新建dataframe
#### 从CSV文件读取
这个文件展示了性别和智商的关系，数据既有分类数据，也有数值数据。

此表中有些值没有，而是用"."来占位，所以需要指定NA = not available是"."。否则后面很难分析。


```python
import pandas
data = pandas.read_csv('data/brain_size.csv', sep=';', na_values=".")  # 因为此文件采用';'，所以需要指明分隔符
data.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Unnamed: 0</th>
      <th>Gender</th>
      <th>FSIQ</th>
      <th>VIQ</th>
      <th>PIQ</th>
      <th>Weight</th>
      <th>Height</th>
      <th>MRI_Count</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>1</td>
      <td>Female</td>
      <td>133</td>
      <td>132</td>
      <td>124</td>
      <td>118.0</td>
      <td>64.5</td>
      <td>816932</td>
    </tr>
    <tr>
      <th>1</th>
      <td>2</td>
      <td>Male</td>
      <td>140</td>
      <td>150</td>
      <td>124</td>
      <td>NaN</td>
      <td>72.5</td>
      <td>1001121</td>
    </tr>
    <tr>
      <th>2</th>
      <td>3</td>
      <td>Male</td>
      <td>139</td>
      <td>123</td>
      <td>150</td>
      <td>143.0</td>
      <td>73.3</td>
      <td>1038437</td>
    </tr>
    <tr>
      <th>3</th>
      <td>4</td>
      <td>Male</td>
      <td>133</td>
      <td>129</td>
      <td>128</td>
      <td>172.0</td>
      <td>68.8</td>
      <td>965353</td>
    </tr>
    <tr>
      <th>4</th>
      <td>5</td>
      <td>Female</td>
      <td>137</td>
      <td>132</td>
      <td>134</td>
      <td>147.0</td>
      <td>65.0</td>
      <td>951545</td>
    </tr>
  </tbody>
</table>
</div>



#### 从数组创建
我们可以把pandas.DataFrame看成是值为存储数组的字典


```python
import numpy as np
x = np.linspace(-6, 6, num=100)
cos, sin = np.cos(x), np.sin(x)
frame = pandas.DataFrame({'x': x, 'cos': cos, 'sin': sin})
frame.head()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>cos</th>
      <th>sin</th>
      <th>x</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0.960170</td>
      <td>0.279415</td>
      <td>-6.000000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>0.919340</td>
      <td>0.393465</td>
      <td>-5.878788</td>
    </tr>
    <tr>
      <th>2</th>
      <td>0.865018</td>
      <td>0.501740</td>
      <td>-5.757576</td>
    </tr>
    <tr>
      <th>3</th>
      <td>0.798003</td>
      <td>0.602653</td>
      <td>-5.636364</td>
    </tr>
    <tr>
      <th>4</th>
      <td>0.719278</td>
      <td>0.694722</td>
      <td>-5.515152</td>
    </tr>
  </tbody>
</table>
</div>



#### pandas.DataFrame也可以从其他格式的文件新建，具体[查阅文档](http://pandas.pydata.org/)

### 操作数据

#### 简单操作


```python
data.shape
```




    (40, 8)


<br>

```python
data.columns
```




    Index(['Unnamed: 0', 'Gender', 'FSIQ', 'VIQ', 'PIQ', 'Weight', 'Height',
           'MRI_Count'],
          dtype='object')



<br>

```python
data['Gender'].head()  # 像字典一样读取
```




    0    Female
    1      Male
    2      Male
    3      Male
    4    Female
    Name: Gender, dtype: object


<br>


```python
data[data['Gender']=='Female']['VIQ'].mean()  # 简单的select，计算性别为女的数据的*VIQ*值得期望
```




    109.45



#### groupby
以分类数据的值分割dataframe


```python
groupby_gender = data.groupby('Gender')
for gender, value in groupby_gender['VIQ']:
    print((gender, value.mean()))
```

    ('Female', 109.45)
    ('Male', 115.25)


<br>

```python
groupby_gender.mean()
```




<div>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>Unnamed: 0</th>
      <th>FSIQ</th>
      <th>VIQ</th>
      <th>PIQ</th>
      <th>Weight</th>
      <th>Height</th>
      <th>MRI_Count</th>
    </tr>
    <tr>
      <th>Gender</th>
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
      <th>Female</th>
      <td>19.65</td>
      <td>111.9</td>
      <td>109.45</td>
      <td>110.45</td>
      <td>137.200000</td>
      <td>65.765000</td>
      <td>862654.6</td>
    </tr>
    <tr>
      <th>Male</th>
      <td>21.35</td>
      <td>115.0</td>
      <td>115.25</td>
      <td>111.60</td>
      <td>166.444444</td>
      <td>71.431579</td>
      <td>954855.4</td>
    </tr>
  </tbody>
</table>
</div>



### 绘制数据
关于各种术语和图形，详情请见[统计术语及图形展示]({% post_url 2017-04-21-statistics-terms %})
#### 绘制箱线图


```python
import matplotlib.pyplot as plt
# Box plots of different columns for each gender
groupby_gender = data.groupby('Gender')
groupby_gender.boxplot(column=['FSIQ', 'VIQ', 'PIQ'])
plt.show()
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2017-04-22-basic-statistics-pandas/output_16_0.png)


#### 绘制散点图
使用Pandas的plotting工具，其实其背后也是matplotlib在工作，所以最后画图需要plt.show()


```python
from pandas.tools import plotting
# Scatter matrices for different columns
plotting.scatter_matrix(data[['Weight', 'Height', 'MRI_Count']])
plotting.scatter_matrix(data[['PIQ', 'VIQ', 'FSIQ']])
plt.show()
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2017-04-22-basic-statistics-pandas/output_18_0.png)



![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2017-04-22-basic-statistics-pandas/output_18_1.png)


## 假设检验

### t检验
#### 单样本检验
`scipy.stats.ttest_1samp()`是t检验的单样本检验，检验样本是否符合零假设。这里就是样本假设期望值和给定值没有显著不同。


```python
from scipy import stats
stats.ttest_1samp(data['VIQ'], 0)  # t test of one sample test
stats.ttest_ind
```




    Ttest_1sampResult(statistic=30.088099970849328, pvalue=1.3289196468728067e-28)



上面计算结果发现p值为1.3e-28，远远小于0.05，说明零假设（样本假设期望值和给定值没有显著不同）不成立，假设期望值和给定值**具有**显著不同

#### 双样本检验
`stats.ttest_ind`是t检验的双样本检验，检验样本是否符合零假设。这里就是假设两个**独立**样本没有显著不同。


```python
female_viq = data[data['Gender'] == 'Female']['VIQ']
male_viq = data[data['Gender'] == 'Male']['VIQ']
stats.ttest_ind(female_viq, male_viq)  
```




    Ttest_indResult(statistic=-0.77261617232750113, pvalue=0.44452876778583217)



上面计算结果发现p值为0.44，远远大于0.05，说明零假设（样本假设期望值和给定值没有显著不同）成立。

#### 配对检验
双样本检验中，我们假设检验两个**独立**样本。如果两个样本不是独立的，那么我们应该使用配对检验（paired test）或叫做重复检验（repeated measures test）。

比如有一个计算机培训，在培训前后分别对每个人做了一个考试，通过对比前后的考试成绩，可以判断培训有无作用。那么我们可以做配对检验，即判断成绩是否有显著不同。可以看出，其实这就是测试两次成绩的差值是否为0。

这里，我们可以计算IQ的三种不同测量方法：PIQ, VIQ, 和FSIQ。我们检验一下FISQ和PIQ是否显著不同，由于我们比较的是相同人在若干次重复检验的问题，所以应该使用配对检验：


```python
stats.ttest_rel(data['FSIQ'], data['PIQ']) 
```




    Ttest_relResult(statistic=1.7842019405859857, pvalue=0.082172638183642358)



根据前文描述，上面的配对检验其实相等于：


```python
stats.ttest_1samp(data['FSIQ'] - data['PIQ'], 0) 
```




    Ttest_1sampResult(statistic=1.7842019405859857, pvalue=0.082172638183642358)



## 线性模型

### 简单线性回归
假设两个观察组，x和y。我们假设y是x的线性函数，下面我们检验这个模型：

$$ y = intercept + coef \times x + e $$

其中*e*是噪声。我们使用statsmodels模块：
* 使用最小二乘法（ordinary least squares，OLS）拟合线性模型。
* 测试coef不为0。


```python
import numpy as np
from statsmodels.formula.api import ols
x = np.linspace(-5, 5, 20)
np.random.seed(1)
# normal distributed noise
y = -5 + 3*x + 4 * np.random.normal(size=x.shape)
# Create a data frame containing all the relevant variables
data = pandas.DataFrame({'x': x, 'y': y})

model = ols("y ~ x", data).fit()
print(model.summary())
```

                                OLS Regression Results                            
    ==============================================================================
    Dep. Variable:                      y   R-squared:                       0.804
    Model:                            OLS   Adj. R-squared:                  0.794
    Method:                 Least Squares   F-statistic:                     74.03
    Date:                Sun, 30 Apr 2017   Prob (F-statistic):           8.56e-08
    Time:                        20:00:29   Log-Likelihood:                -57.988
    No. Observations:                  20   AIC:                             120.0
    Df Residuals:                      18   BIC:                             122.0
    Df Model:                           1                                         
    Covariance Type:            nonrobust                                         
    ==============================================================================
                     coef    std err          t      P>|t|      [0.025      0.975]
    ------------------------------------------------------------------------------
    Intercept     -5.5335      1.036     -5.342      0.000      -7.710      -3.357
    x              2.9369      0.341      8.604      0.000       2.220       3.654
    ==============================================================================
    Omnibus:                        0.100   Durbin-Watson:                   2.956
    Prob(Omnibus):                  0.951   Jarque-Bera (JB):                0.322
    Skew:                          -0.058   Prob(JB):                        0.851
    Kurtosis:                       2.390   Cond. No.                         3.03
    ==============================================================================
    
    Warnings:
    [1] Standard Errors assume that the covariance matrix of the errors is correctly specified.


根据数据结果可见，拟合的结果是intercept为-5.53，coef是2.94。

类似的，我们可以看看性别和智商的关系：


```python
VIQ = data['VIQ']
Gender = data['Gender']
model = ols("VIQ ~ Gender", data).fit()
print(model.summary())
```

                                OLS Regression Results                            
    ==============================================================================
    Dep. Variable:                    VIQ   R-squared:                       0.015
    Model:                            OLS   Adj. R-squared:                 -0.010
    Method:                 Least Squares   F-statistic:                    0.5969
    Date:                Sun, 30 Apr 2017   Prob (F-statistic):              0.445
    Time:                        20:15:35   Log-Likelihood:                -182.42
    No. Observations:                  40   AIC:                             368.8
    Df Residuals:                      38   BIC:                             372.2
    Df Model:                           1                                         
    Covariance Type:            nonrobust                                         
    ==================================================================================
                         coef    std err          t      P>|t|      [0.025      0.975]
    ----------------------------------------------------------------------------------
    Intercept        109.4500      5.308     20.619      0.000      98.704     120.196
    Gender[T.Male]     5.8000      7.507      0.773      0.445      -9.397      20.997
    ==============================================================================
    Omnibus:                       26.188   Durbin-Watson:                   1.709
    Prob(Omnibus):                  0.000   Jarque-Bera (JB):                3.703
    Skew:                           0.010   Prob(JB):                        0.157
    Kurtosis:                       1.510   Cond. No.                         2.62
    ==============================================================================
    
    Warnings:
    [1] Standard Errors assume that the covariance matrix of the errors is correctly specified.


由上面拟合结果可见，智商和性别没有关系。因为R-squared很小。

### 多元线性回归
看一看[文档吧](http://www.statsmodels.org/stable/index.html)，我写到机器学习的时候在仔细说这方便的东西。
