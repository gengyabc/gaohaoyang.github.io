---
layout: post
title:  "线性代数 1 - 图像"
categories: 线性代数
tags:  数学
mathjax: true
author: Geng
---

* content
{:toc}
这部分主要就是洗脑吧.

我不知道别人, 我在上大学的时候, 老师不管三七二十一, 上来就是看不懂的各种矩阵定义, 公式啥的, 最后基本通过背公式过了线性代数, 然后发誓再也不要碰这个东西了. 现在听了 MIT 老爷爷的课, 感觉发誓还是不靠谱的, 又被老爷爷忽悠来学习了.






## 洗脑

### 二元一次方程

这个方程:

$$
\begin{equation} \begin{cases} 
2x - y = 0 \\
-x + 2y = 3
\end{cases} \end{equation}
$$

写成矩阵形式:

$$
\begin{equation}
\begin{bmatrix}
2 & -1 \\
-1 & 2  
\end{bmatrix}
\begin{bmatrix}
x \\
y
\end{bmatrix}
=
\begin{bmatrix}
0 \\
3
\end{bmatrix}
\end{equation}
$$

我们可以将这个矩阵形式看成 \\(系数矩阵 \times 未知向量 = 结果向量\\)

### 行图像

我们知道, 方程组(1)可以在平面中画出两个直线:
![]({{ site.url }}/assets/images/posts/machineLearning/线性代数/2017-10-07-brain-wash/row.png)

未知向量就是这两条直线的交点.

### 列图像

我们将上面方程组按照列组合, 可以写作:

$$
\begin{equation}
x
\begin{bmatrix}
2  \\
-1  \\
\end{bmatrix}
+y
\begin{bmatrix}
-1  \\
2  
\end{bmatrix}
=
\begin{bmatrix}
0 \\
3
\end{bmatrix}
\end{equation}
$$

列组合情况下, 可以方程看做是列向量的线性组合(因为作图软件问题, 下图 a 为 x 取值, b 为 y 取值). 当 *y* 取值固定, 比如 \\(y = 2\\) 时, 不同 *x* 取值会产生不同向量:

![]({{ site.url }}/assets/images/posts/machineLearning/线性代数/2017-10-07-brain-wash/vector1.gif)

同样的, 当 *x* 取值固定, 比如 \\(x = 1\\) 时, 不同 *y* 取值也会产生不同向量:

![]({{ site.url }}/assets/images/posts/machineLearning/线性代数/2017-10-07-brain-wash/vector2.gif)

根据上面的两张动图, 可以明显看出来, *x, y* 取值分别为 *1, 2* 时, 方程(3)两边相等.


### 三元一次方程

高阶的情况类似, 比如下面这个方程组:

$$
\begin{equation} \begin{cases} 
2x - y = 0 \\
-x + 2y -z = -1 \\
-3y + 4z = 4
\end{cases} \end{equation}
$$

他的行图像就是三个平面在三维空间的情形. 从下图来看, 已经有点看晕了吧.
![]({{ site.url }}/assets/images/posts/machineLearning/线性代数/2017-10-07-brain-wash/plane.png)

但是如果从列图像来看, 就简单许多:

$$
\begin{equation}
x
\begin{bmatrix}
2  \\
-1  \\
0
\end{bmatrix}
+y
\begin{bmatrix}
-1  \\
2  \\
-3
\end{bmatrix}
+z
\begin{bmatrix}
0  \\
-1  \\
4
\end{bmatrix}
=
\begin{bmatrix}
0 \\
-1 \\
4
\end{bmatrix}
\end{equation}
$$

![]({{ site.url }}/assets/images/posts/machineLearning/线性代数/2017-10-07-brain-wash/vector3.png)

比较"凑巧"的是, 这个例子中结果向量也是 \\((0, -1, 4)^T\\)(这里使用行向量的转置来记录列向量)

单从图像上看, 我们可以发现列图像更简单, 对于我们这些凡夫俗子来说, 简单就是好.

### 线性组合
现在考虑这个问题:

* 对于 \\(\mathbf{A}\vec{X}=\vec{b}\\) , 任取一个 \\(\vec{b}\\) 都会有解吗?

或者这样考虑这个问题:

* \\(\mathbf{A}\\) 的各种线性组合, 可以填满整个三维空间吗?

对于上面的例子是可以的, 但是有的情况就不行. 比如三个列向量如果都在一个平面内, 那就没办法填满这个三维空间了. 

## 向量基础
经过上面行图像和列图像的洗脑, 我们应该习惯开始用列图像来思考了吧. 下面就是一些基本的概念了.

对于线性代数的概念, 理解的时候最好在脑子里出一张图, 至少二维的图能出一张吧?

### 线性组合
\\(c \vec{v} + d \vec{w}\\) 就是一个简单的线性组合(两个向量都是列向量). 只要两个向量不在一个平面, 那么他们就能通过不同组合布满整个平面.

### 点乘(内积)
点乘是向量几何性质(长度,角度)的变换:

假设 \\(\vec{v}=(v_1, v_2)\\), \\(\vec{w}=(w_1, w_2)\\), 他们的夹角为\\(\theta\\):
\begin{equation}
\vec{v} \cdot \vec{w} = v_1 w_1 + v_2 w_2 = ||\vec{v}|| \cdot ||\vec{w}|| \cdot cos\theta
\end{equation}

### Python 计算


```python
import numpy as np

v = np.array([2, 3, 4])
w = np.array([1, 1, 1])

# 标量与向量乘积
u = 2*v + 3*w
u
```




array([ 7,  9, 11])




```python
# 点乘
u = np.dot(v, w)
u
```




9



## 矩阵基础

### 从向量到矩阵
现在假设有三个列向量: \\(\vec{u}=(1, -1, 0)^T\\), \\(\vec{v}=(0, 1, -1)^T\\), \\(\vec{w}=(0, 0, 1)^T\\), 他们在三维空间的组合可以记为:

$$
\begin{equation}
c
\begin{bmatrix}
1 \\
-1 \\
0 
\end{bmatrix}
+d
\begin{bmatrix}
0 \\
1 \\
-1 
\end{bmatrix}
+e
\begin{bmatrix}
0 \\
0 \\
1 
\end{bmatrix}
=
\begin{bmatrix}
c \\
d-c \\
e-d 
\end{bmatrix}
\end{equation}
$$

现在, 我们使用**矩阵来重写这个组合**. 那么现在可以将方程(7)写为:

$$
\begin{equation}
\begin{bmatrix}
1 & 0 & 0 \\
-1 & 1 & 0 \\
0 & -1 & 1
\end{bmatrix}
\begin{bmatrix}
c \\
d \\
e
\end{bmatrix}
=
\begin{bmatrix}
c \\
d-c \\
e-d 
\end{bmatrix}
\end{equation}
$$

现在有一个向量 \\(\vec{x}\\) 为 \\( (c, d, e)^T \\), 那么:

$$
\begin{equation}
\mathbf{A}\vec{x}=
\begin{bmatrix}
\vec{u} & \vec{v} & \vec{w}
\end{bmatrix}
\begin{bmatrix}
c \\
d \\
e
\end{bmatrix}
= c\vec{u} + d\vec{v} + e\vec{w} = \vec{b}
\end{equation}
$$

现在我们的视角需要有所变换了: 开始的时候, 数字 *c, d, e* 与向量相乘; 现在, 矩阵与这些数字相乘. 

> 注意现在的视角不是点乘的视角. 点乘的时候, 我们想象的是矩阵的每一行, 分别乘以列向量. 而现在的视角是每一列的线性组合

### Python 计算


```python
import numpy as np

A = np.array([[1, 2, 3],
[2, 5, 2],
[6, -3, 1]])
x = np.array([[0],
[0],
[2]])

b = np.dot(A, x)
b
```




array([[6],
[4],
[2]])



