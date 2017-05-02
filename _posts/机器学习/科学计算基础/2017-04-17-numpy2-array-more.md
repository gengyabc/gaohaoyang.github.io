---
layout: post
title:  "科学计算3 - 矩阵运算深入"
categories: 科学计算
tags:  Numpy
author: Geng
---

* content
{:toc}


## 与标量的计算
每一个元素与标量运算


```python
import numpy as np
lst = [2, 3, 7.9, 3.3, 6.9, 0.11, 10.3, 12.9]
v = np.array(lst)
```






```python
print(v + 2)
```

    [  4.     5.     9.9    5.3    8.9    2.11  12.3   14.9 ]

<br>

```python
print(v - 1.38)
```

    [  0.62   1.62   6.52   1.92   5.52  -1.27   8.92  11.52]


<br>

```python
print(v * 2.2)
```

    [  4.4     6.6    17.38    7.26   15.18    0.242  22.66   28.38 ]


<br>

```python
print(v ** 2)
```

    [  4.00000000e+00   9.00000000e+00   6.24100000e+01   1.08900000e+01
       4.76100000e+01   1.21000000e-02   1.06090000e+02   1.66410000e+02]


## 数组运算
元素之间的运算


```python
A = np.array([ [11, 12, 13], [21, 22, 23], [31, 32, 33] ])
B = np.ones((3,3))
print("相加: ")
print(A + B)
print("\n相乘: ")
print(A * (B + 1))
```

    相加: 
    [[ 12.  13.  14.]
     [ 22.  23.  24.]
     [ 32.  33.  34.]]
    
    相乘: 
    [[ 22.  24.  26.]
     [ 42.  44.  46.]
     [ 62.  64.  66.]]


## 矩阵点乘
### 一种方法就是使用numpy的dot()方法


```python
print(np.dot(A, B))
```

    [[ 36.  36.  36.]
     [ 66.  66.  66.]
     [ 96.  96.  96.]]


### 另一种方法是将numpy数组转为numpy矩阵。

numpy数组运算是元素对元素的运算：


```python
A = np.array([ [1, 2, 3], [2, 2, 2], [3, 3, 3] ])
B = np.array([ [3, 2, 1], [1, 2, 3], [-1, -2, -3] ])
R = A * B
print(R)
```

    [[ 3  4  3]
     [ 2  4  6]
     [-3 -6 -9]]


如果将其转为numpy矩阵的话，直接用乘法运算符即可：


```python
MA = np.mat(A)
MB = np.mat(B)
R = MA * MB
print(R)
```

    [[ 2  0 -2]
     [ 6  4  2]
     [ 9  6  3]]


<br>

```python
print(np.dot(MA, MB))
```

    [[ 2  0 -2]
     [ 6  4  2]
     [ 9  6  3]]


<br>

```python
print(np.dot(A, B))
```

    [[ 2  0 -2]
     [ 6  4  2]
     [ 9  6  3]]


## 广播（Broadcasting）
广播是numpy提供的一项强大功能，简单说就是脑补。

一个大数组，一个小数组，大数组通过广播功能（脑补），变成一个同样形状的大数组，然后进行运算。

我们通过例子看下


```python
import numpy as np
A = np.array([ [11, 12, 13], [21, 22, 23], [31, 32, 33] ])
B = np.array([1, 2, 3])
print("Multiplication with broadcasting: ")
print(A * B)
print("... and now addition with broadcasting: ")
print(A + B)
```

    Multiplication with broadcasting: 
    [[11 24 39]
     [21 44 69]
     [31 64 99]]
    ... and now addition with broadcasting: 
    [[12 14 16]
     [22 24 26]
     [32 34 36]]


脑补过程：

![](http://www.python-course.eu/images/broadcasting_example_1.png)

以上过程相当于B进行了如下计算：


```python
B = np.array([[1, 2, 3]] * 3)
print(B)
```

    [[1 2 3]
     [1 2 3]
     [1 2 3]]


然后在看看其他例子

首先将B转置


```python
B = np.array([1, 2, 3])
B[:, np.newaxis]
```




    array([[1],
           [2],
           [3]])




```python
A * B[:, np.newaxis]
```




    array([[11, 12, 13],
           [42, 44, 46],
           [93, 96, 99]])



脑补过程：

![](http://www.python-course.eu/images/broadcasting_example_2.png)

有了上面经验，下面这个大脑补就不难理解了


```python
A = np.array([10, 20, 30])
B = np.array([1, 2, 3])
A[:, np.newaxis]
A[:, np.newaxis] * B
```




    array([[10, 20, 30],
           [20, 40, 60],
           [30, 60, 90]])



过程如下：

![](http://www.python-course.eu/images/broadcasting_example_3.png)
