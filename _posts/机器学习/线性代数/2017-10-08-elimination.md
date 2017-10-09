---
layout: post
title:  "线性代数 2 - 消元法"
categories: 线性代数
tags:  数学
mathjax: true
author: Geng
---

* content
{:toc}

这部分主要是消元法, 也就是我们很熟悉的解方程的那个消元法. 在运用消元法的过程中, 更加深入的体会上次内容的**图像**, 同时认识到怎样运用这些图像去更好的理解单位矩阵.

这次内容主要使用行图像.






## 消元法思想

我们解多元一次方程的时候, 都会使用到消元法, 这个方法可以理解为:
1. 将方程变为上三角形系统
2. 使用回带的方法求解各个未知数

例如:

$$
\begin{equation}
\begin{cases} 
x - 2y = 1 \\
3x + 2y = 11
\end{cases}
\end{equation}
$$

首先变为上三角形系统: 第二个方程减去第一个方程的三倍(下面方程可以看成一个倒立的三角形):

$$
\begin{equation}
\begin{cases} 
x &-&2y &= 1 \\
  & &8y &= 8
\end{cases}
\end{equation}
$$

然后从下到上一个未知数一个未知数的求解. 这个过程大家都很熟悉了, 下面要做的就是用矩阵的方法来实现这个步骤

### 方程组和矩阵

我们来求一个稍微复杂一点的方程组

$$
\begin{equation} \begin{cases} 
 x + 2y + z = 2 \\
 3x + 8y + z = 12 \\
 4y + z = 2
\end{cases} \end{equation}
$$

写为矩阵 \\(\vec{A} \vec{x} = \vec{b} \\) 的形式:

$$
\begin{equation}
\begin{bmatrix}
1 & 2 & 1 \\
3 & 8 & 1 \\
0 & 4 & 1
\end{bmatrix}
\begin{bmatrix}
x \\
y \\
z
\end{bmatrix}
=
\begin{bmatrix}
2 \\
12 \\
2
\end{bmatrix}
\end{equation}
$$

### 主元

我们将注意力集中到系数矩阵 \\(\vec{A}\\), 其中左上角的 *1* 是消元法的第一个**主元**(Pivot 1).

> 主元: 使用消元法的行的第一个非零元素

### 消元

首先, 将第一列除了主元的元素全都变成0. 方法就是熟悉的消元法: 第一行不变, 第二行减去第一行的三倍, 第三行在这个例子中不需要变:

$$
\begin{equation}
\begin{bmatrix}
1 & 2 & 1 \\
3 & 8 & 1 \\
0 & 4 & 1
\end{bmatrix}
\xrightarrow{(2,1)}
\begin{bmatrix}
1 & 2 & 1 \\
0 & 2 & -2 \\
0 & 4 & 1
\end{bmatrix}
\end{equation}
$$

上面的 \\((2,1)\\) 是标记要消去的是第二行第一列的那一项.

然后类似的方法, 消去第二个主元, 得到一个上三角矩阵\\(U\\):

$$
\begin{equation}
U=
\begin{bmatrix}
1 & 2 & 1 \\
0 & 2 & -2 \\
0 & 0 & 5
\end{bmatrix}
\end{equation}
$$

### 回带求解
这个时候我们需要使用增广矩阵来求解, 对于方程(3)和(4)来说, 其增广矩阵为:

$$
\begin{equation}
\begin{bmatrix}
1 & 2 & 1 & 2 \\
3 & 8 & 1 & 12 \\
0 & 4 & 1 & 2
\end{bmatrix}
\end{equation}
$$

也就是将矩阵 \\(\vec{A}\\) 和向量 \\(\vec{b}\\) 拼接起来的矩阵. 在使用消元法求解的时候, 使用增广矩阵就可以方便的求解:

$$
\begin{equation}
\begin{bmatrix}
1 & 2 & 1 & 2 \\
3 & 8 & 1 & 12 \\
0 & 4 & 1 & 2
\end{bmatrix}
\xrightarrow{(2,1)}
\begin{bmatrix}
1 & 2 & 1 & 2 \\
0 & 2 & -2 & 6 \\
0 & 4 & 1 & 2
\end{bmatrix}
\xrightarrow{(3,2)}
\begin{bmatrix}
1 & 2 & 1 & 2 \\
0 & 2 & -2 & 6 \\
0 & 0 & 5 & -10
\end{bmatrix}
\end{equation}
$$

这样一来, 就可以自后向前, 先求出 *z*, 再求 *y*, 最后求得 *x*.

## 矩阵与消元

### 向量与矩阵的乘法

上次内容已经介绍过行图像和列图像的内容. 这里再强调一下.

\\(\vec{A} \times \overrightarrow{列向量x}\\) 产生一个列向量. 新产生的列向量的每一项是 \\(\vec{A}\\) 的列在 \\(\overrightarrow{列向量x}\\) 作用下的线性组合

$$
\begin{equation}
\begin{bmatrix}
x & x & x \\
x & x & x \\
x & x & x
\end{bmatrix}
\begin{bmatrix}
3 \\
4 \\
5
\end{bmatrix}
=
\begin{bmatrix}
col1 & col2 & col3 
\end{bmatrix}
\begin{bmatrix}
3 \\
4 \\
5
\end{bmatrix}
=
3 \times col1 + 4 \times col2 + 5 \times col3
\end{equation}
$$

![]({{ site.url }}/assets/images/posts/machineLearning/线性代数\2017-10-08-elimination\col_mul.png)

\\(\overrightarrow{行向量x} \times \vec{A}\\) 产生一个行向量. 新产生的行向量的每一项是 \\(\vec{A}\\) 的行在 \\(\overrightarrow{行向量x}\\) 作用下的线性组合


$$
\begin{equation}
\begin{bmatrix}
1 & 2 & 7
\end{bmatrix}
\begin{bmatrix}
x & x & x \\
x & x & x \\
x & x & x
\end{bmatrix}
=
\begin{bmatrix}
1 & 2 & 7
\end{bmatrix}
\begin{bmatrix}
row1 \\
row2 \\
row3
\end{bmatrix}
=
1 \times row1 + 2 \times row2 + 7 \times row3
\end{equation}
$$

![]({{ site.url }}/assets/images/posts/machineLearning/线性代数\2017-10-08-elimination\row_mul.png)

### 单位矩阵

根据上面的回顾, 我们来看下面这个运算:

$$
\begin{equation}
\begin{bmatrix}
1 & 0 & 0 \\
0 & 1 & 0 \\
0 & 0 & 1
\end{bmatrix}
\begin{bmatrix}
3 \\
4 \\
5
\end{bmatrix}
=
\begin{bmatrix}
col1 & col2 & col3 
\end{bmatrix}
\begin{bmatrix}
3 \\
4 \\
5
\end{bmatrix}
=
3 \times \begin{bmatrix}
1  \\
0  \\
0 
\end{bmatrix} + 4 \times \begin{bmatrix}
 0  \\
 1  \\
 0 
\end{bmatrix} + 5 \times \begin{bmatrix}
 0 \\
 0 \\
 1
\end{bmatrix}
=
\begin{bmatrix}
3 \\
4 \\
5
\end{bmatrix}
\end{equation}
$$

看起来什么都没有变是吗? 我们仔细看就会发现, 最后结果列向量的第一项只受 \\([1, 0, 0]^T\\) 的影响, 而这个影响就是列向量 \\(\vec{x}\\) 的第一项. 其他项影响可以图示为:

![]({{ site.url }}/assets/images/posts/machineLearning/线性代数\2017-10-08-elimination\col.png)

列向量乘以这个特殊的矩阵后没有发生变化, 这个特殊的矩阵就是**单位矩阵**, 记为 \\(\vec{I}\\). 同样, 行向量乘以单位矩阵也没有变化

$$
\begin{equation}
\begin{bmatrix}
1 & 2 & 7
\end{bmatrix}
\begin{bmatrix}
1 & 0 & 0 \\
0 & 1 & 0 \\
0 & 0 & 1
\end{bmatrix}
=
\begin{bmatrix}
1 & 2 & 7
\end{bmatrix}
\begin{bmatrix}
row1 \\
row2 \\
row3
\end{bmatrix}
=
1 \times \begin{bmatrix} 1 & 0 & 0 \end{bmatrix} + 
2 \times \begin{bmatrix} 0 & 1 & 0 \end{bmatrix} +
7 \times \begin{bmatrix} 0 & 0 & 1 \end{bmatrix}
\end{equation}
$$



### 消元矩阵

有了单位矩阵的知识储备, 我看再来看 式(5). 我把它重写如下:

$$
\begin{equation}
\begin{bmatrix}
1 & 2 & 1 \\
3 & 8 & 1 \\
0 & 4 & 1
\end{bmatrix}
\xrightarrow[第二行减去第一行的三倍]{(2,1)}
\begin{bmatrix}
1 & 2 & 1 \\
0 & 2 & -2 \\
0 & 4 & 1
\end{bmatrix}
\end{equation}
$$

我们可以通过将第一个矩阵乘上一个什么矩阵, 成为后面那个矩阵吗? 

首先注意到, 第一行和最后一行没有变化, 那么根据上面单位矩阵的讨论, 暂时可以写为:

$$
\begin{equation}
\begin{bmatrix}
1 & 0 & 0 \\
x & x & x \\
0 & 0 & 1
\end{bmatrix}
\begin{bmatrix}
1 & 2 & 1 \\
3 & 8 & 1 \\
0 & 4 & 1
\end{bmatrix}
=
\begin{bmatrix}
1 & 2 & 1 \\
0 & 2 & -2 \\
0 & 4 & 1
\end{bmatrix}
\end{equation}
$$

现在剩下的工作是怎么样实现第二行减去第一行的三倍: \\(-3 \times 第一行 + 第二行\\)

这个时候, 根据 式(10), (12) 行向量的作用, 剩下的未知数第一个是对第一行的操作(乘以 *-3*), 第二个是对第二行的操作(直接复制), 第三个是对第三行的操作(不存在影响). 那么剩下的未知部分可以写为: (-3, 1, 0), 可以得到:

$$
\begin{equation}
\vec{E_{21}}\vec{A}=
\begin{bmatrix}
1 & 0 & 0 \\
-3 & 1 & 0 \\
0 & 0 & 1
\end{bmatrix}
\begin{bmatrix}
1 & 2 & 1 \\
3 & 8 & 1 \\
0 & 4 & 1
\end{bmatrix}
=
\begin{bmatrix}
1 & 2 & 1 \\
0 & 2 & -2 \\
0 & 4 & 1
\end{bmatrix}
\end{equation}
$$

下一步就是对第三行操作了, 同样思路, 下面要做的是第三行减去第二行的两倍, 即:

$$
\begin{equation}
\vec{E_{32}}\vec{E_{21}}\vec{A}=
\begin{bmatrix}
1 & 0 & 0 \\
0 & 1 & 0 \\
0 & -2 & 1
\end{bmatrix}
\begin{bmatrix}
1 & 0 & 0 \\
-3 & 1 & 0 \\
0 & 0 & 1
\end{bmatrix}
\begin{bmatrix}
1 & 2 & 1 \\
3 & 8 & 1 \\
0 & 4 & 1
\end{bmatrix}
=
\begin{bmatrix}
1 & 2 & 1 \\
0 & 2 & -2 \\
0 & 0 & 5
\end{bmatrix}
= \vec{U}(上三角矩阵)
\end{equation}
$$

将 \\(\vec{E_{32}}\vec{E_{21}}\\) 看做一个向量 \\(\vec{E}\\), 这个就是**消元矩阵**

## 逆矩阵

我们刚才考虑的是怎样通过消元矩阵, 使得 \\(\vec{A}\\) 变成 \\(\vec{U}\\). 那么如何使得 \\(\vec{E}\\) 变成 \\(\vec{A}\\) 呢?

根据 式(9) ~ (12), 我们应该可以归纳出:

* 左乘变换矩阵行变换
* 右乘变换矩阵列变换

那我们现在就考虑一个 \\(\vec{E_{21}}\\) 复原的问题. 它的作用是第二行减去第一行的三倍, 那么其反作用就是第二行加上第一行的三倍

$$
\begin{equation}
\begin{bmatrix}
1 & 0 & 0 \\
3 & 1 & 0 \\
0 & 0 & 1
\end{bmatrix}
\begin{bmatrix}
1 & 0 & 0 \\
-3 & 1 & 0 \\
0 & 0 & 1
\end{bmatrix}
=\begin{bmatrix}
1 & 0 & 0 \\
0 & 1 & 0 \\
0 & 0 & 1
\end{bmatrix}
\end{equation}
$$

这个式子就是

$$
\begin{equation}
\vec{E_{21}^{-1}}\vec{E_{21}} =\vec{I}
\end{equation}
$$

这个 \\(\vec{E_{21}^{-1}}\\) 就是 \\(\vec{E_{21}^{-1}}\\) 的逆矩阵了.
