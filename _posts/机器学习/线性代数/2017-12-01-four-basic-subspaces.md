---
layout: post
title:  "线性代数 10 - 四个基本子空间"
categories: 线性代数
tags:  数学
mathjax: true
author: Geng
---

* content
{:toc}
在熟悉了秩和维数后, 我们将要综合四个子空间, 来看看线性代数的核心. 秩就是主元的数目, 子空间的维数就是基的向量的个数.

秩揭示了四个子空间的维数, 这四个子空间是:






* 行空间 $C(A^T) \in R^n$ (即 $R^n$ 的子空间), $dim C(A^T)=r$


* 列空间 $C(A) \in R^m$ (即 $R^m$ 的子空间), $dim C(A)=r$，主元所在的列即可组成列空间的一组基。


* 零空间 $N(A) \in R^n$ (即 $R^n$ 的子空间), $dim N(A)=n-r$，自由元所在的列即可组成零空间的一组基。


* 左零空间 $N(A^T) \in R^m$ (即 $R^m$ 的子空间), $dim N(A^T)=m-r$

其中, 矩阵 $A$ 为 $m \times n$，$rank(A)=r$

对于列空间和零空间, 之前已经有了分析, 这里不再赘述

先看一个例子, 对于矩阵 $A$, 消元后变为最简形式 $R$:

$$
\begin{equation}
A=
\begin{bmatrix}
1 & 2 & 3 & 1 \\
1 & 1 & 2 & 1 \\
1 & 2 & 3 & 1 \\
\end{bmatrix}
\underrightarrow{消元、化简}
\begin{bmatrix}
1 & 0 & 1 & 1 \\
0 & 1 & 1 & 0 \\
0 & 0 & 0 & 0 \\
\end{bmatrix}
=R
\end{equation}
$$

## 行空间

由于我们做了行变换，所以 $A$ 的列空间受到影响，$C(R) \neq C(A)$，而行变换并不影响行空间，所以可以在 $R$ 中看出前两行就是行空间的一组基。

所以，可以得出无论对于矩阵 $A$ 还是 $R$，其行空间的一组基，可以由 $R$ 矩阵的前 $r$ 行向量组成（这里的 $R$ 就是第七讲提到的简化行阶梯形式）。

## 左零空间

对于左零空间，有 $A^Ty=0 \rightarrow (A^Ty)^T=0^T\rightarrow y^TA=0^T$，因此得名。

那么, 我们需要将矩阵转置, 然后从头开始计算左零空间吗? 我们已经做过 $A$ 到 $R$ 的化简, 我们先看看这个过程能不能给我们答案.

采用Gauss-Jordan消元，将增广矩阵 $\left[\begin{array}{c\|c}A_{m \times n} & I_{m \times m}\end{array}\right]$ 中 $A$ 的部分划为简化行阶梯形式 $\left[\begin{array}{c\|c}R_{m \times n} & E_{m \times m}\end{array}\right]$，此时矩阵 $E$ 会将所有的行变换记录下来。

则 $EA=R$，而在前几讲中，有当 $A'$ 是 $m$ 阶可逆方阵时，$R'$ 即是 $I$，所以 $E$ 就是 $A^{-1}$。


本例中

$$
\begin{equation}
\left[\begin{array}{c|c}A_{m \times n} & I_{m \times m}\end{array}\right]=
\left[
\begin{array}
{c c c c|c c c}
1 & 2 & 3 & 1 & 1 & 0 & 0 \\
1 & 1 & 2 & 1 & 0 & 1 & 0 \\
1 & 2 & 3 & 1 & 0 & 0 & 1 \\
\end{array}
\right]
\underrightarrow{消元、化简}
\left[
\begin{array}
{c c c c|c c c}
1 & 0 & 1 & 1 & -1 & 2 & 0 \\
0 & 1 & 1 & 0 & 1 & -1 & 0 \\
0 & 0 & 0 & 0 & -1 & 0 & 1 \\
\end{array}
\right]
=\left[\begin{array}{c|c}R_{m \times n} & E_{m \times m}\end{array}\right]
\end{equation}
$$

则

$$
\begin{equation}
EA=
\begin{bmatrix}
-1 & 2  & 0 \\
1  & -1 & 0 \\
-1 & 0  & 1 \\
\end{bmatrix}
\cdot
\begin{bmatrix}
1 & 2 & 3 & 1 \\
1 & 1 & 2 & 1 \\
1 & 2 & 3 & 1 \\
\end{bmatrix}
=
\begin{bmatrix}
1 & 0 & 1 & 1 \\
0 & 1 & 1 & 0 \\
0 & 0 & 0 & 0 \\
\end{bmatrix}
=R
\end{equation}
$$


很明显，式中$E$的最后一行对$A$的行做线性组合后，得到$R$的最后一行，即$0$向量，也就是$y^TA=0^T$。


