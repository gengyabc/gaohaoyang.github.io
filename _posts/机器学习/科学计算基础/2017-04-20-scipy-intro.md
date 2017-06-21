---
layout: post
title:  "科学计算6 - Scipy基本介绍"
categories: 科学计算
tags:  Scipy
author: Geng
---

* content
{:toc}


前面已经介绍，Scipy在Numpy基础上扩展了更多功能，比如回归，傅里叶变换等。

## 简单介绍


| 模块 | 简介 |
|----------|--------------|
|scipy.cluster	|Vector quantization / Kmeans|
|scipy.constants|	Physical and mathematical constants|
|scipy.fftpack	|Fourier transform|
|scipy.integrate|	Integration routines|
|scipy.interpolate	|Interpolation|
|scipy.io	|Data input and output|
|scipy.linalg	|Linear algebra routines|
|scipy.ndimage|	n-dimensional image package|
|scipy.odr|	Orthogonal distance regression|
|scipy.optimize|	Optimization|
|scipy.signal|	Signal processing|
|scipy.sparse|	Sparse matrices|
|scipy.spatial|	Spatial data structures and algorithms|
|scipy.special|	Any special mathematical functions|
|scipy.stats	|Statistics|

以上模块都基于Numpy，不过基本上都是互相独立的，我们以前引入Numpy使用下列方法：





```python
import numpy as np
```
不过，引入Scipy一般如下方法：
```python
from scipy import stats  # from scipy import some-module
```
如果引入名称容易混淆，一般如下操作：
```python
from scipy import io as spio
```
> Scipy命名空间中会有Numpy方法（比如scipy.cos就是np.cos)，但是他们只是因为历史原因而存在

## 模块不完全备忘录
如果太全面，我就不想看了，这里只列出简单的应用

### scipy.special
包括一些常用特殊函数：
* Bessel function, such as **scipy.special.jn()** (nth integer order Bessel function)
* Elliptic function (**scipy.special.ellipj()** for the Jacobian elliptic function, ...)
* Gamma function: **scipy.special.gamma()**, also note scipy.special.gammaln() which will give the log of Gamma to a higher numerical precision.
* Erf, the area under a Gaussian curve: **scipy.special.erf()**

### scipy.optimize

主要是解决寻找极值或者判断相等的问题

#### 极值
可以使用`optimize.basinhopping()`方法求最小值

例如下面的例子，这个方程有全局最小值-1.3和局部最小值3.8。在寻找全局最小值的时候，普通算法容易陷入局部最小值


```python
from scipy import optimize
import numpy as np
import matplotlib.pyplot as plt

def f(x):
    return x**2 + 10*np.sin(x)

x = np.arange(-10, 10, 0.1)
plt.plot(x, f(x)) 
plt.show()
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2017-04-20-scipy-intro/output_5_0.png)



```python
result = optimize.basinhopping(f, 0)
print(result)
```

                            fun: -7.945823375615284
     lowest_optimization_result:       fun: -7.945823375615284
     hess_inv: array([[ 0.08581052]])
          jac: array([  1.19209290e-07])
      message: 'Optimization terminated successfully.'
         nfev: 15
          nit: 3
         njev: 5
       status: 0
      success: True
            x: array([-1.30644])
                        message: ['requested number of basinhopping iterations completed successfully']
          minimization_failures: 0
                           nfev: 1539
                            nit: 100
                           njev: 513
                              x: array([-1.30644])



```python
result.x
```




    array([-1.30644001])



#### 根
使用`scipy.optimize.fsolve()`来求根


```python
root = optimize.fsolve(f, 1)  # 初始值为1
print(root)
```

    [ 0.]


但是需要注意，这里只知道一个根，根据上图，在-2.5的位置应该也有一个根，我们可以尝试修改初始值来求解


```python
root2 = optimize.fsolve(f, -2)
print(root2)
```

    [-2.47948183]


#### 拟合
假设我们对\\(f(x)\\)进行了取样，但是有些噪声引入：


```python
xdata = np.linspace(-10, 10, num=20)
np.random.seed(1234)
ydata = f(xdata) + np.random.randn(xdata.size)
```

假设我们已知\\(f(x)\\)的形式，只是不知道系数，我们可以使用最小二乘法求解系数


```python
def f2(x, a, b):
    return a*x**2 + b*np.sin(x)

guess = [2, 2]  # 系数的猜测值
params, params_covariance = optimize.curve_fit(f2, xdata, ydata, guess)


fig = plt.figure()
ax = fig.add_subplot(111)
ax.plot(x, f(x), 'b-', label="f(x)")
ax.plot(x, f2(x, *params), 'r--', label="Curve fit result")
ax.plot(-10, -20, 'go', label="Minima")
roots = np.array([root, root2])
ax.plot(roots, f(roots), 'kv', label="Roots")
ax.legend()
ax.set_xlabel('x')
ax.set_ylabel('f(x)')
plt.show()
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2017-04-20-scipy-intro/output_15_0.png)


### scipy.stats
这个模块包含各种统计学工具。我们可以使用Numpy的随机数方法生成随机数。

#### 概率密度函数和直方图


```python
from scipy import stats
import matplotlib.pyplot as plt

a = np.random.normal(size=10000)
bins = np.linspace(-5, 5, 30)
histogram, bins = np.histogram(a, bins=bins, normed=True)  # pyplot也有一个pyplot.hist()画直方图
bins = 0.5*(bins[1:] + bins[:-1])


b = stats.norm.pdf(bins)  # pdf：概率密度函数，这里是画出正态分布的pdf

plt.plot(bins, histogram)
plt.plot(bins, b)

plt.show()
```


![]({{ site.url }}/assets/images/posts/machineLearning/科学计算基础/2017-04-20-scipy-intro/output_17_0.png)


如果我们知道一个随机过程符合正态分布，我们可以通过拟合来估计参数：


```python
loc, std = stats.norm.fit(a)
print(loc)
print(std)
```

    0.00811240037816
    1.00235737446


#### 百分比 Percentiles
我们既可以使用Numpy的方法，也可以使用Scipy的方法


```python
np.median(a) # Numpy计算中位数
```




    0.017162639954891042

<br>


```python
stats.scoreatpercentile(a, 50) # Scipy计算中位数
```




    0.017162639954891042



<br>

```python
stats.scoreatpercentile(a, 90)  # Scipy计算90%
```




    1.3024079013456049



<br>

```python
np.percentile(a, 90)  # Numpy计算90%
```




    1.3024079013456049



## 更多内容，用到了再说
