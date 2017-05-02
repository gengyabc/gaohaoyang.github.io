---
layout: post
title:  "科学计算4 - 随机与概率"
categories: 科学计算
tags:  Numpy
author: Geng
---

* content
{:toc}


## 随机数
我们可以使用Python的ramdom()方法，不过这里我们使用Numpy的random()方法。先看代码：






```python
import numpy as np
np.random.seed(0)
np.random.random(10)
```




    array([ 0.5488135 ,  0.71518937,  0.60276338,  0.54488318,  0.4236548 ,
            0.64589411,  0.43758721,  0.891773  ,  0.96366276,  0.38344152])


<br>

```python
np.random.seed(0)
np.random.random(10)
```




    array([ 0.5488135 ,  0.71518937,  0.60276338,  0.54488318,  0.4236548 ,
            0.64589411,  0.43758721,  0.891773  ,  0.96366276,  0.38344152])



首先我们可以看到，使用np.random.random(10)生成了十个随机数。这些随机数符合**连续平均分布**。

但是注意，通过使用相同的seed(n)，我们生成的随机数是相同的！

我们可以使用matplotlib画出我们生成的随机数，以便有一个直观映像。关于matplotlib，请确定已经安装。


```python
%matplotlib inline
import matplotlib.pyplot as plt
x = np.arange(0, 1, 0.001)
np.random.seed(0)
y = np.random.random(1000)
plt.plot(x, y)
plt.show()
```


![]({{ site.url }}/assets/images/posts/machineLearning/2017-04-15-sci-intro/output_5_0.png)


### 随机整数
```python
numpy.random.randint(low, high=None, size=None)
```


```python
print(np.random.randint(1, 7))
print(np.random.randint(1, 7, size=3))
```

    5
    [5 3 3]


## 各种分布
### 平均分布
我么使用直方图来展示下面分布。

> 以下内容，如果没有什么难度，不再摘抄官方文档或者解释，自己查阅文档即可。如果使用Jupyter的话，直接shift+tab即可快速阅读文档。


```python
values = np.random.uniform(-10.0, 10.0, 100000)
plt.hist(values, 50)
plt.show()
```


![]({{ site.url }}/assets/images/posts/machineLearning/2017-04-15-sci-intro/output_9_0.png)


### 正态分布


```python
mu = 5.0  # 期望
sigma = 2.0  # 标准差
values = np.random.normal(mu, sigma, 10000)
plt.hist(values, 50)
plt.show()
```


![]({{ site.url }}/assets/images/posts/machineLearning/2017-04-15-sci-intro/output_11_0.png)


## 求解基本的概率问题

### 期望


```python
values.mean()
# 或者
np.mean(values)  # 这个形式比较容易和后面的统一，相对好记
```




    5.0030194542513033



### 中位数


```python
np.median(values)  # values.median()不能运行
```




    4.9983038429561848



### 标准差


```python
np.std(values)
```




    1.9979958260724584



### 方差


```python
np.var(values)
```




    3.9919873210029655



### 还有更多可用，但是都写出来就太烦人了，用到了自己查就好

## 随机选择
有时候我们需要从一个数组中选一个元素，如果不用随机索引元素位置的话，可以这样：


```python
possible_destinations = ["Berlin", "Hamburg", "Munich", 
                         "Amsterdam", "London", "Paris", 
                         "Zurich", "Heidelberg", "Strasbourg", 
                         "Augsburg", "Milan", "Rome"]
print(np.random.choice(possible_destinations))
```

    Heidelberg


<br>

```python
x1 = np.random.choice(possible_destinations, size=3)  # 返回长度为3的向量
print(x1)
x2 = np.random.choice(possible_destinations, size=(3, 4))  # 返回3 * 4数组
print(x2)
print(type(x2))
```

    ['Zurich' 'Paris' 'London']
    [['Rome' 'Paris' 'London' 'Amsterdam']
     ['Hamburg' 'Strasbourg' 'London' 'Augsburg']
     ['Milan' 'Heidelberg' 'London' 'Berlin']]
    <class 'numpy.ndarray'>

