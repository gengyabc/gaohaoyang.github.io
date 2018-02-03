---
layout: post
title:  "线性代数 15 - 子空间投影"
categories: 线性代数
tags:  数学
mathjax: true
author: Geng
---

* content
{:toc}

三维空间中:

1. 一个向量在 $z$ 轴的投影是什么呢? 它在 $xy$ 平面的投影呢? 
2. 我可以使用怎样的矩阵来投影呢? 






这部分内容就是要解决这两个问题, 不过第一个问题我们头脑里应该已经有图像了. 当一个向量 $b$ 投影到一个直线时, 这个投影 $p$ 就是 $b$ 沿着这条线的分量. 当一个向量 $b$ 投影到一个平面时, 这个投影 $p$ 就是 $b$ 沿着这个平面的分量. 投影 $p=Pb$, $P$就是投影矩阵 

如果想要找到空间中所有子空间的 $p$ 和 $P$, 使得 $p=Pb$ 应该怎么办? 首先, 投影到的子空间是什么?

为了更好的描述子空间, 我们使用基. 我们将基向量作为矩阵 $A$ 的列向量, 那么我们就是在投影到 $A$ 的列空间: $p=Pb = Ax$

## 投影到一条直线

如下图所示: $b$ 投影在 $a$ 上. 其中重要的是其中的正交: 从 $b$ 到 投影 $p$ 的向量 $e$ 垂直于 $a$. 

![]({{ site.url }}/assets/images/posts/machineLearning/线性代数/2017-12-07-projection/pro1.png)

投影 $p$ 可以是 $a$ 的倍数, 可以写为 $p=\hat{x}a$. 然后根据 $p=Pb$, 可以得到投影矩阵 $P$. 这里的问题是 $\hat{x}$ 是什么?

因为 $e=b-\hat{x}a$ 垂直于 $a$, 那么 $a \cdot (b-\hat{x}a) = 0$, 可以解得:

$$
\begin{equation}
\hat{x} = \dfrac {a \cdot b} {a \cdot a} = \dfrac {a^T b} {a^Ta}
\end{equation}
$$

$$
\begin{equation}
\begin{aligned}
p &= \hat{x}a = a \hat{x} \\
&= a \dfrac {a^T b} {a^Ta}  \\
&= \dfrac {a a^T} {a^Ta} b  \\
&= Pb
\end{aligned}
\end{equation}
$$

* 如果 $b=a$ 会发生什么? $p=Pa=a$
* 如果 $b$ 和 $a$ 垂直呢? 那么 $a^Tb=0$, $p=0$

> 这里列向量 $a$ 和行向量 $a^T$ 相乘, 得到一个秩一矩阵, $a^Ta$ 是一个数字, $P$ 由于是一个列向量与行向量的乘积, 秩为一.

> 如果 $a$ 变成 $2a$, $P$ 不变.

> $P^2=P$ 而且 $P^T=P$

> $I-P$ 也是一个投影. 它是上图的 $e$ 那条边. 而且 $P(I-P) = P-P=0$, $P$ 和 $I-P$ 的投影的子空间正交

## 投影到一个子空间

一图解千言, 先上图再说:

![]({{ site.url }}/assets/images/posts/machineLearning/线性代数/2017-12-07-projection/pro2.png)

$A$ 是一个 $m \times n$ 矩阵, $b$ 投影在 $A$ 的列空间 $S$. $A$ 和 $b$ 都在 $R^m$ 中

$A$ 的列向量有 $n$ 个, 在 $R^m$ 中并且线性无关, 分别为: $a_1, \cdots a_n$. 

$R^m$ 中的 $b$ 在 $a_1, \cdots a_n$ 的生成空间的投影就是 $p$. 找到一个 $p=\hat{x}_1 a_1 + \cdots + \hat{x}_n a_n$, 使其最接近 $b$

> 注意这里的 $A$ 是一个长矩阵, 行数大于列数, $m>n$

我们在 $A$ 的列空间中, 找不到一个向量满足: $Ax=b$; 只能找到一个 $p=A\hat{x}$, 使其最近接 $b$. 这个 $\hat{}$ 表示这只是一个最接近的列向量.

现在, 我们就是要计算 $R^m$ 中的向量 $b$, 投影在子空间 $R^n$( $S$ ): 首先找到向量 $\hat{x}$, 然后找到投影 $p=A\hat{x}$, 最后就找到 $P$了. $e$ 向量垂直于子空间. 那么:

$$
\begin{equation}
a_1^T(b-A\hat{x})=0 \\
\vdots \\
a_n^T(b-A\hat{x})=0
\end{equation}
$$

也就是

$$
\begin{equation}
\begin{bmatrix}
a_1^T \\
\vdots \\
a_n^T
\end{bmatrix}
(b-A\hat{x})=
\begin{bmatrix}0
\end{bmatrix}
\end{equation}
$$

可以写成:

$$
\begin{equation}
A^T(b-A\hat{x}) = 0
\end{equation}
$$

得到这部分最重要的公式:

$$
\begin{equation}
\color{red} {A^T A \hat{x} = A^T b}
\end{equation}
$$

经过计算可得:

$$
\begin{equation}
p=A\hat x=A(A^TA)^{-1}A^Tb \\
P=A(A^TA)^{-1}A^T
\end{equation}
$$

> $A^{-1}$ 是不存在的, 因为 $A$ 不是方阵

> 当且仅当 $A$ 的列线性无关时, $A^TA$ 可逆, 而且它是对称方阵

### 正交

由式 (5) 可知, $b-A\hat{x}$ (也就是$e$) 在 $A^T$ 的零空间中. 零空间与行向量正交, 所以 $e$ 一定与 $A^T$ 的行向量正交, 垂直于 $A$ 的列向量.


```python

```
