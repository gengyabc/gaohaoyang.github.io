---
layout: post
title:  "科学计算2 - 矩阵运算介绍"
categories: 科学计算
tags:  Numpy
author: Geng
---

* content
{:toc}


这部分先介绍科学计算最基本的矩阵运算，或者说是数组运算。我也不知道一般怎么说，知道我在说啥就行了。
Numpy可以创建标量（0维），一维和多维数组。下面就看看怎么做。





## 建立矩阵

### 标量（0维数组）
标量可以看做是0维数组


```python
import numpy as np
x = np.array(42)
print("x: ", x)
print("x类型为: ", type(x))
print("x维度为:", np.ndim(x))
```

    x:  42
    x类型为:  <class 'numpy.ndarray'>
    x维度为: 0


### 一维数组
一维数组一般称作**向量**，Numpy数组中的数据是**相同数据类型**的，类型由**dtype**决定。


```python
F = np.array([1, 1, 2, 3, 5, 8, 13, 21])
V = np.array([3.4, 6.9, 99.8, 12.8])
print("F: ", F)
print("V: ", V)
print("F数据类型: ", F.dtype)
print("V数据类型: ", V.dtype)
print("F维度: ", np.ndim(F))
print("V维度: ", np.ndim(V))
```

    F:  [ 1  1  2  3  5  8 13 21]
    V:  [  3.4   6.9  99.8  12.8]
    F数据类型:  int64
    V数据类型:  float64
    F维度:  1
    V维度:  1


可以这样快速构建：


```python
np.array([3, 4] * 4)
```




    array([3, 4, 3, 4, 3, 4, 3, 4])



## 多维数组
和Matlab类似的方法，可以创建多维数组


```python
A = np.array([ [3.4, 8.7, 9.9], 
               [1.1, -7.8, -0.7],
               [4.1, 12.3, 4.8]])
print(A)
print(A.ndim)
```

    [[  3.4   8.7   9.9]
     [  1.1  -7.8  -0.7]
     [  4.1  12.3   4.8]]
    2



```python
B = np.array([ [[111, 112], [121, 122]],
               [[211, 212], [221, 222]],
               [[311, 312], [321, 322]] ])
print(B)
print(B.ndim)
```

    [[[111 112]
      [121 122]]
    
     [[211 212]
      [221 222]]
    
     [[311 312]
      [321 322]]]
    3


可以这样快速构建：


```python
np.full((2,2),7)
```




    array([[7, 7],
           [7, 7]])



或者这样


```python
np.array([[1, 2]] * 4)
```




    array([[1, 2],
           [1, 2],
           [1, 2],
           [1, 2]])



## 数组形状
**shape**方法返回数组的形状。对于二维数组来说，就是长宽是多少。**shape**返回的是一个tuple，如果是(6, 3)，那么就说有6行3列。


```python
x = np.array([ [67, 63, 87],
               [77, 69, 59],
               [85, 87, 99],
               [79, 72, 71],
               [63, 89, 93],
               [68, 92, 78]])
print(np.shape(x))
```

    (6, 3)


或者这样写：


```python
print(x.shape)
```

    (6, 3)


你甚至可以修改形状，但是前提是保持数组大小不变：


```python
x.shape = (3, 6)
print(x)
```

    [[67 63 87 77 69 59]
     [85 87 99 79 72 71]
     [63 89 93 68 92 78]]


## 数组操作
数组元素的赋值和索引是数组操作的基础。学过任何编程语言，都应该对这部分不陌生。这里简单看一下代码好了。

### 索引


```python
F = np.array([1, 1, 2, 3, 5, 8, 13, 21])
# print the first element of F, i.e. the element with the index 0
print(F[0])
# print the last element of F
print(F[-1])
B = np.array([ [[111, 112], [121, 122]],
               [[211, 212], [221, 222]],
               [[311, 312], [321, 322]] ])
print('这是一种索引结果:',  B[1][1][0])
# 或者这样索引
print('这样索引结果一样:', B[1, 1, 0])
```

    1
    21
    这是一种索引结果: 221
    这样索引结果一样: 221


上面使用了两种索引方法，其中**第二种效率更高**，而且写法更简单。

### 切割
语法很简单：`A[start:stop:step]`


```python
S = np.array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])
print(S[2:5])
print(S[:4])
print(S[6:])
print(S[:])
```

    [2 3 4]
    [0 1 2 3]
    [6 7 8 9]
    [0 1 2 3 4 5 6 7 8 9]



```python
A = np.array([
[11,12,13,14,15],
[21,22,23,24,25],
[31,32,33,34,35],
[41,42,43,44,45],
[51,52,53,54,55]])
print(A[:3,2:])
```

    [[13 14 15]
     [23 24 25]
     [33 34 35]]


下面看看第三个参数step的作用，创建一个二维数组：


```python
X = np.arange(28).reshape(4,7)
print(X)
```

    [[ 0  1  2  3  4  5  6]
     [ 7  8  9 10 11 12 13]
     [14 15 16 17 18 19 20]
     [21 22 23 24 25 26 27]]



```python
print(X[::2, ::3])
```

    [[ 0  3  6]
     [14 17 20]]


> 注意：虽然对list和tuple的切割建立新的对象，但是对数组的切割，仅仅是创建了一种查看数组的快捷方式（View）。通过这种快捷方式，你可以更清楚的观察想要观察的内容，但是如果修改被观察的内容，原始数组的数据也会改变。


```python
A = np.array([0, 1, 2, 3, 4, 5, 6, 7, 8, 9])
S = A[2:6]
S[0] = 22
S[1] = 23
print(A)
```

    [ 0  1 22 23  4  5  6  7  8  9]


同样的事情，发生在Python的list，就完全不一样了：


```python
lst = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
complete_new_list = lst[2:6]
complete_new_list[0] = 22
complete_new_list[1] = 23
print(lst)
```

    [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]


## 0和1组成的矩阵
和Matlab很像，看看代码吧。


```python
E = np.ones((2,3))
print(E)
F = np.ones((3,4),dtype=int)
print(F)
```

    [[ 1.  1.  1.]
     [ 1.  1.  1.]]
    [[1 1 1 1]
     [1 1 1 1]
     [1 1 1 1]]



```python
Z = np.zeros((2,4))
print(Z)
```

    [[ 0.  0.  0.  0.]
     [ 0.  0.  0.  0.]]


Numpy也允许你根据已有的一个数组，建立和它相同shape的0或者1数组。使用`ones_like(a)`和`zeros_like(a)`即可


```python
x = np.array([2,5,18,14,4])
E = np.ones_like(x)
print(E)
Z = np.zeros_like(x)
print(Z)
```

    [1 1 1 1 1]
    [0 0 0 0 0]


## 单位矩阵
可以使用：
* identy
* eye


```python
np.identity(4)
```




    array([[ 1.,  0.,  0.,  0.],
           [ 0.,  1.,  0.,  0.],
           [ 0.,  0.,  1.,  0.],
           [ 0.,  0.,  0.,  1.]])




```python
np.eye(5, 8, k=1, dtype=int)
```




    array([[0, 1, 0, 0, 0, 0, 0, 0],
           [0, 0, 1, 0, 0, 0, 0, 0],
           [0, 0, 0, 1, 0, 0, 0, 0],
           [0, 0, 0, 0, 1, 0, 0, 0],
           [0, 0, 0, 0, 0, 1, 0, 0]])



其中*k*参数可以看下图理

![](http://www.python-course.eu/images/eye_principle_of_operation.png)

## dtype
**dtype**型的对象是`numpy.dtype`类的实例，可以用`numpy.dtype`创建。
我们已经使用过int和float作为numpy的dtype的数据类型。不过dtype有更强大的能力，我们可以使用它创建结构化数组"Structured Arrays"。例如下表所示的表格中的一列，第一行是标题，后面是数据。

|Country|Population Density|Area|Population|
| ------------- | ------------- | ------------- |--------|
|Netherlands|393|41526|16,928,800|
|Belgium|337|30510|11,007,020|
|...|...|...|...|




```python
i16 = np.dtype(np.int16)  # 创建了一个名为i16的数据类型
print(i16)
lst = [ [3.4, 8.7, 9.9], 
        [1.1, -7.8, -0.7],
        [4.1, 12.3, 4.8] ]
A = np.array(lst, dtype=i16)  # A数组是i16类型的
print(A)
```

    int16
    [[ 3  8  9]
     [ 1 -7  0]
     [ 4 12  4]]



```python
# 结构化的数组类型，名为'density'，是np.int32类型。
dt = np.dtype([('density', np.int32)])
# 采用这个数据类型创建一个numpy数组
x = np.array([(393,), (337,), (256,)],
             dtype=dt)
print(x)
print("\nThe internal representation:")
print(repr(x))
```

    [(393,) (337,) (256,)]
    
    The internal representation:
    array([(393,), (337,), (256,)], 
          dtype=[('density', '<i4')])


想要访问这部分内容就很方便了：


```python
print(x['density'])
```

    [393 337 256]


> 关于"<"和">"是[大端(Big Endian)与小端(Little Endian)的问题](http://www.cppblog.com/Sandywin/archive/2008/12/22/70064.html)

> [更多dtype](http://www.python-course.eu/numpy_dtype.php)
