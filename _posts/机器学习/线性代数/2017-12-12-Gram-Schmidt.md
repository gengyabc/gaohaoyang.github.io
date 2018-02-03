---
layout: post
title:  "线性代数 17 - 正交矩阵和Gram-Schmidt正交化法"
categories: 线性代数
tags:  数学
mathjax: true
author: Geng
---

* content
{:toc}


这部分有两个目的: 
* 一个是认识到正交如何简化寻找 $\hat{x}$, $p$ 和 $P$. 这有什么好处呢? 这意味着 $A^TA$ 是一个对角矩阵.
* 另一个是如何构造正交向量. 构造的正交矩阵记为 $Q$.





## 标准正交向量

标准正交向量（orthonormal）为:

$$
\begin{equation}
q_i^Tq_j=
\begin{cases}
0\quad i\neq j \\ 
1\quad i=j
\end{cases}
\end{equation}
$$


以标准正交向量为列的矩阵, 就是标准正交矩阵:

$$
\begin{equation}
Q=\Bigg[q_1 q_2 \cdots q_n\Bigg]
\end{equation}
$$

标准正交矩阵很好用, 因为 $Q^TQ=I$ (从公式 1 可以很简单的推导出来)

特别的，当 $Q$ 恰好是方阵时, 由于正交性, $Q$ 是可逆的，又 $Q^TQ=I$，所以 $Q^T=Q^{-1}$

## 标准正交矩阵的作用

前面一直在讲投影矩阵, 那么标准正交矩阵怎样帮助投影矩阵呢?

投影矩阵有 $P=A(A^TA)^{-1}A^T$, 那么如果 $A=Q$, $P=QQ^T$. 如果 $Q$是方阵, $P=I$. 也就对于方阵，其列向量是标准正交的，则其列空间就是整个向量空间，而投影到整个空间的投影矩阵就是单位矩阵

将一个矩阵投影到整个空间不是很无聊? 但是如果 $p=b$, $b$ 将会由它的一维投影组成. 如果 $q_1, ..., q_n$ 是整个空间的标准正交基, 那么 $b=QQ^Tb$ 是它在 $q$ 方向的分量之和:

$$
\begin{equation}
b = (q_1q_1^T +...+ q_nq_n^T)b \\
b = q_1(q_1^Tb)+...+ q_n(q_n^Tb)
\end{equation}
$$

这个是傅里叶变换的基础, 其实也是所有数学变换的基础. 它将向量组分解成正交的分量, 然后反变换的时候再将这些分量相加就得到了原始向量组

对于最小二乘法问题, 计算 $A^TA\hat x=A^Tb$ 变为计算 $Q^TQ\hat x=Q^Tb$，也就是 $\hat x=Q^Tb$.

对于每个分量: $\hat x_i=q_i^Tb$。如果我们知道标准正交基，则解向量第 $i$ 个分量为: 基的第 $i$ 个分量乘以 $b$，在第 $i$ 个基方向上的投影就等于 $q_i^Tb$。

## Gram-Schmidt正交化

Gram-Schmidt正交化: 两个线性无关的向量 $a, b$，先把它们化为正交向量 $A, B$，再将它们单位化，变为单位正交向量 $q_1=\frac{A}{\left\|A\right\|}, q_2=\frac{B}{\left\|B\right\|}$：

* 取 $a$ 向量的方向，$a=A$；
* 接下来将 $b$ 投影在 $A$ 的法方向上得到 $B$，也就是求子空间投影一讲中，我们提到的误差向量 $e=b-p$，即 $B=b-\frac{A^Tb}{A^TA}A$ x=p$。）

如果有三个线性无关的向量 $a, b, c$，则要求它们的正交向量 $A, B, C$，再将它们单位化，变为单位正交向量$q_1=\frac{A}{\left\|A\right\|}, q_2=\frac{B}{\left\|B\right\|}, q_3=\frac{C}{\left\|C\right\|}$：

* 前两个向量我们已经得到了，我们现在需要求第三个向量同时正交于 $A, B$；
* 依然沿用上面的方法，从 $c$ 中减去其在 $A, B$ 上的分量，得到正交与 $A, B$ 的 $C$：$C=c-\frac{A^Tc}{A^TA}A-\frac{B^Tc}{B^TB}B$。

## $A=QR$ 分解

我们曾经用矩阵的眼光审视消元法，有 $A=LU$。同样的，我们也用矩阵表达标准正交化，$A=QR$。

设矩阵 $A$ 有两个列向量 $\Bigg[a_1 a_2\Bigg]$，则标准正交化后有$\Bigg[a_1 a_2\Bigg]=\Bigg[q_1 q_2\Bigg]\begin{bmatrix}a_1^Tq_1&a_2^Tq_1\\\\a_1^Tq_2&a_2^Tq_2\end{bmatrix}$，而左下角的$a_1^Tq_2$始终为$0$，因为Gram-Schmidt正交化总是使得$a_1\bot q_2$，后来构造的向量总是正交于先前的向量。所以这个$R$矩阵是一个上三角矩阵。

由 $A=QR$ 可得: $R=Q^TA$ 和 $A^TA=R^TQ^TQR=R^TR$, 那么 $A^TA\hat{x}=A^Tb$ 简化为: $R\hat{x}=Q^Tb$. 这个求解过程会快得多. 

记住有 $A=QR$ 分解, 以后用到了知道在干啥, 为什么就够了.


