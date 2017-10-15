---
layout: post
title:  "线性代数 3 - 矩阵乘法和逆矩阵"
categories: 线性代数
tags:  数学
mathjax: true
author: Geng
---

前面内容介绍了向量与矩阵的乘法, 最多涉及到一些单位矩阵与其他矩阵的简单乘法. 这部分主要介绍矩阵乘法,逆矩阵和求解逆矩阵的方法.





## 矩阵乘法
这里提供了五种乘法的思路, 可以从不同方面考虑矩阵的乘法

### 行点乘列(行列内积)
这个方法是最常见, 每个学过线性代数的同学都在用的方法.

$m \times n$ 矩阵 $\mathbf{A}$ 和 $n\times p$ 矩阵 $\mathbf{B}$（$\mathbf{A}$ 的行数与 $\mathbf{B}$ 的列数相等），两矩阵相乘得到 $\mathbf{A}\mathbf{B}=\mathbf{C}$, 其中 $\mathbf{C}$ 是 $m\times p$ 的矩阵，对于 $\mathbf{C}$ 矩阵中的第 $i$ 行第 $j$ 列元素 $c_{ij}$，有：

$$
\begin{equation}
c_{ij}=row_i\cdot column_j=\sum_{k=i}^na_{ik}b_{kj}
\end{equation}
$$

其中 $a_{ik}$ 是 $\mathbf{A}$ 的第 $i$ 行第 $k$ 列元素，$b_{kj}$ 是 $\mathbf{B}$ 的第 $k$ 行第 $j$ 列元素。

大家大学要考过线性代数, 这部分内容都知道吧? $c_{ij}$ 是$\mathbf{A}$ 第 $i$ 行点乘 $\mathbf{B}$ 第 $j$ 列:

$$
\begin{equation}
\begin{bmatrix}
    &\vdots&\\
    &row_i&\\
    &\vdots&
\end{bmatrix}
\begin{bmatrix}
    && \\
    \cdots&column_j&\cdots \\
    && 
\end{bmatrix}
    =
\begin{bmatrix}
    &\vdots&\\
    \cdots&c_{ij}&\cdots\\
    &\vdots&
\end{bmatrix}
\end{equation}
$$

### 整列考虑

$$
\begin{equation}
\begin{bmatrix}
    & &  \\
    & A &\\
    & &
\end{bmatrix}
\begin{bmatrix}
| & || &  & \vdots  &  \\
| & || &  & \vdots  &  \\
\vdots & \vdots & B & \vdots &  \\
| & || &  & \vdots  & 
\end{bmatrix}
=
\mathbf{C}
\end{equation}
$$

$\mathbf{A}$ 和 $\mathbf{B}$ 还是满足上文的条件, 那这个时候, 就是考虑 $\mathbf{B}$ 的每一列与 $\mathbf{A}$ 相乘, 最后结果 $\mathbf{C}$ 的第 $i$ 列只受 $\mathbf{B}$ 的第 $i$ 列的影响.

注意到, $\mathbf{A}$ 各列长度都是 $m$, $\mathbf{C}$各列长度也是 $m$, $\mathbf{C}$ 的每一列, 其实就是 $\mathbf{A}$ 各列的线性组合. 而 $\mathbf{B}$ 中的数字相当于告诉我们, 这个线性组合是什么样的


![]({{ site.url }}/assets/images/posts/machineLearning/线性代数/2017-10-10-matrix-multiplication/col.png)

换种说法, 观看上图, 我们可以认为 $\mathbf{A}$ 是某种产品的原材料, 而  $\mathbf{B}$ 是这种产品的材料配比, 产品  $\mathbf{C}$ 的每一列看成是一种子产品, 那么每种子产品原材料都是一样的, 不一样的只是这些原材料的配比而已. 每一列的子产品配比, 都由配比 $\mathbf{B}$ 对应列来决定.  

### 整行考虑

思路和整列考虑没有什么不同, 简单来说就是考虑 $\mathbf{A}$ 的每一行与 $\mathbf{B}$ 相乘, 最后结果 $\mathbf{C}$ 的第 $i$ 行只受 $\mathbf{A}$ 的第 $i$ 行的影响.

而且, $\mathbf{B}$ 各行长度都是 $p$, $\mathbf{C}$各行长度也是 $p$, $\mathbf{C}$ 的每一行, 其实就是 $\mathbf{B}$ 各行的线性组合. 而 $\mathbf{A}$ 中的数字相当于告诉我们, 这个线性组合是什么样的

### 分块考虑

分块考虑的话，就是把原始矩阵看作是几个子矩阵的组合，每个子矩阵都认为是和原来的一项一样，然后按照上面说的方法进行计算就好了。比如

$$
\begin{equation} 
\left[ \begin{array}{c|c}A_1&A_2 \\ 
\hline 
A_3&A_4\end{array} \right] 
\left[\begin{array}{c|c}B_1&B_2 \\
\hline 
B_3&B_4\end{array}\right]
=
\left[\begin{array}{c|c}A_1B_1+A_2B_3&A_1B_2+A_2B_4 \\
\hline A_3B_1+A_4B_3&A_3B_2+A_4B_4\end{array} \right]
\end{equation}
$$

可以看出，计算的时候，就当这些子矩阵是普通矩阵元素即可，很多情况下，分块可以简化运算。

### 列乘以行

原课程中，这部分是在**分块考虑**之前的，但是可能我没有题解透彻，我觉得这部分放在这里更好理解

先从简单的开始看, $m \times 1$ 矩阵 $\mathbf{A}=\begin{bmatrix}2 \\ 3 \\ 4 \end{bmatrix}$ 和 $1\times p$ 矩阵 $\mathbf{B}=\begin{bmatrix}1 & 6 \end{bmatrix}$, 

$$
\begin{equation} 
 \mathbf{A} \mathbf{B} =
 \begin{bmatrix}2 \\ 3 \\ 4 \end{bmatrix} 
 \begin{bmatrix}1 & 6 \end{bmatrix}
 =
 \begin{bmatrix}
 2 & 12 \\
 3 & 18 \\
 4 & 24
 \end{bmatrix}
\end{equation}
$$

不管是行图像考虑, 还是列图像考虑, 都是行或者列, 乘以对应的元素, 生成新的矩阵. 

这里只从列图像考虑, 行图像自己对应看即可. 重要的是注意到, 产生的矩阵每一列都和向量$\begin{bmatrix}2 & 3 & 4 \end{bmatrix}^T $ 同向

如果 $\mathbf{A}=\begin{bmatrix}2 & 7 \\ 3 & 8\\ 4 & 9 \end{bmatrix}$,  $\mathbf{B}=\begin{bmatrix}1 & 6 \\ 0 & 0 \end{bmatrix}$, 那么采用上面的**分块考虑**的思想，可以将 $\mathbf{A}$ 按列分块，将 $\mathbf{B}$ 按行分块如下：

$$ \begin{equation} 
\mathbf{A}=\begin{bmatrix}2 & 7 \\ 3 & 8\\ 4 & 9 \end{bmatrix} = \begin{bmatrix} \mathbf{A_1} & \mathbf{A_2} \end{bmatrix} \ \ \ 
\mathbf{B}=\begin{bmatrix}1 & 6 \\ 0 & 0 \end{bmatrix} = \begin{bmatrix} \mathbf{B_1} \\ \mathbf{B_2} \end{bmatrix}
\end{equation} $$

那么，可以看到，$\mathbf{A}\mathbf{B} = \mathbf{A_1}\mathbf{B_1} + \mathbf{A_2}\mathbf{B_2} $ 

## 逆矩阵

这里只考虑**方阵**。

首先要明确，不是所有的方阵都有逆，如果有逆，那么 $\mathbf{A^{-1}} \mathbf{A} = \mathbf{I} = \mathbf{A} \mathbf{A^{-1}}$. 如果是方阵，左右逆矩阵相等；如果非方阵，那么左右逆矩阵不相等，因为那样的话因为行列数目的限制，没有办法相乘了。

> 矩阵 $\mathbf{A}$ 可以看作是 $\mathbf{A}=\mathbf{A}\mathbf{I}$, 即 $\mathbf{A}$ 对 $\mathbf{I}$ 做了一个线性变换, 结果为 $\mathbf{A}$. 那么 $\mathbf{A^{-1}}$ 可以这样理解: $\mathbf{A}$ 经过 $\mathbf{A^{-1}}$ 的作用, 又变回了 $\mathbf{I}$

对于这些有逆的矩阵，我们称其为**可逆的或非奇异**的。我们先来看看**奇异矩阵**（不可逆的）：

$$ \begin{equation} 
A=\begin{bmatrix}1&3\\2&6\end{bmatrix}
\end{equation} $$

观察这个方阵，我们如果用另一个矩阵乘 $\mathbf{A}$ ，则得到的结果矩阵中的每一列应该都是 $\begin{bmatrix}1\\\\2\end{bmatrix}$ 的倍数，也就是这两个列向量不管怎么线性组合, 都是在一个方向上的, 无法产生单位矩阵那种正交的效果, 也就是不存在逆矩阵, 使其可以再变回 $\mathbf{I}$ 如下图所示:

![]({{ site.url }}/assets/images/posts/machineLearning/线性代数/2017-10-10-matrix-multiplication/coline.png)

上面的图示其实说明: **如果存在非零向量 $\vec{x}$, 使得 $\mathbf{A}\vec{x} = 0 $, 那么 $\mathbf{A}$ 没有逆矩阵**. 为什么呢? $\mathbf{A}\vec{x}$ 可以看做是列向量 $\vec{x}$ 作用下 矩阵 $\mathbf{A}$ 各列的线性组合, 那么有一个非零的 $\vec{x}$, 使得 $\mathbf{A}$ 各列经过长度变化后相加减, 变成了零, 矩阵各列如果不在一条直线上, 必须要和 $\vec{0}$ 相乘才可以, 和条件矛盾, 那么 $\mathbf{A}$ 的各列只能是同向或者反向的.  

## 求解逆矩阵

这里使用**高斯-若尔当**方法,该方法可以一次处理整个矩阵. 现在有这个矩阵 $ \mathbf{A}=\begin{bmatrix}1 & 3 \\\\ 2 & 7 \end{bmatrix}$, 写为增广矩阵的形式: $\left[\begin{array}{cc\|cc}1&3&1&0\\\\2&7&0&1\end{array}\right]$, 接下来用消元法将左侧变为单位矩阵:

$$ \begin{equation} 
\left[\begin{array}{cc|cc}1&3&1&0\\2&7&0&1\end{array}\right]\xrightarrow{row_2-2row_1}\left[\begin{array}{cc\|cc}1&3&1&0\\0&1&-2&1\end{array}\right]\xrightarrow{row_1-3row_2}\left[\begin{array}{cc\|cc}1&0&7&-3\\0&1&-2&1\end{array}\right]
\end{equation} $$

这样，我们就将矩阵从 $\left[\begin{array}{c\|c} \mathbf{A} & \mathbf{I} \end{array}\right]$ 变为 $\left[\begin{array}{c\|c} \mathbf{I}& \mathbf{A^{-1}}\end{array}\right]$

而高斯-若尔当法的本质是使用消元矩阵 $\mathbf{E}$，对 $\mathbf{A}$ 进行操作，$\mathbf{E}\left[\begin{array}{c\|c}\mathbf{A}&\mathbf{I}\end{array}\right]$. 使用消元方法, $\mathbf{E}\mathbf{A}=\mathbf{I}$，进而得到 $\left[\begin{array}{c\|c}\mathbf{I}&\mathbf{E}\end{array}\right]$，其实这个消元矩阵 $\mathbf{E}$ 就是 $\mathbf{A^{-1}}$，而高斯-若尔当法中的 $\mathbf{I}$ 只是负责记录消元的每一步操作，待消元完成，逆矩阵就自然出现了。
