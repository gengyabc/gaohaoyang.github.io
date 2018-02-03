---
layout: post
title:  "线性代数 8 - 求解 Ax=b 可解性和解的结构"
categories: 线性代数
tags:  数学
mathjax: true
author: Geng
---

* content
{:toc}


前面知道了 $Ax=0$ 的解和零空间, 这里继续讨论 $Ax=b$ 的问题, 然后继续研究秩.

## 求解 $Ax=b$

我们将会完整解出线性方程组 $Ax=b$, 继续上次的例子:





$$
A=
\begin{bmatrix}
1 & 2 & 2 & 2\\
2 & 4 & 6 & 8\\
3 & 6 & 8 & 10\\
\end{bmatrix}
$$

求 $Ax=b$ 的解：

### 消元
写出其增广矩阵（augmented matrix）$\left[\begin{array}{c|c}A & b\end{array}\right]$, 然后消元：

$$
\begin{equation}
\left[
\begin{array}{c c c c|c}
1 & 2 & 2 & 2 & b_1 \\
2 & 4 & 6 & 8 & b_2 \\
3 & 6 & 8 & 10 & b_3 \\
\end{array}
\right]
\underrightarrow{消元}
\left[
\begin{array}{c c c c|c}
1 & 2 & 2 & 2 & b_1 \\
0 & 0 & 2 & 4 & b_2-2b_1 \\
0 & 0 & 0 & 0 & b_3-b_2-b_1 \\
\end{array}
\right]
\end{equation}
$$

观察最后一行, 可以发现, 有解的必要条件为 $b_3-b_2-b_1=0$.

那么 $b$ 满足什么条件才能让方程 $Ax=b$ 有解呢?

从列图像考虑, 当且仅当 $b$ 属于 $A$ 的列空间时成立.

从行图像考虑, 如果 $A$ 的各行线性组合得到 $0$ 行, 则 $b$ 中分量做同样的线性组合, 结果也为 $0$ 时，方程才有解.

### 求解方法

对于 $Ax=b$, 可以将其分为:

$$
\begin{equation}
\begin{cases} 
Ax_p =b \\
Ax_n = 0
\end{cases}
\end{equation}
$$

为什么呢? 上面方程组可以加起来, 写为 $A(x_p+x_n)=b$

我们现在要做的首先就是求出零空间内的特殊解, 然后求出满足 $Ax_p=b$ 的一个**特解**, **通解**则是特解与零空间中任意向量之和, 是这个方程组的所有的解. 那么路线图有了, 开始干活:

1. 求零空间特殊解(详见[07. 求解 Ax=0，主变量，特殊解]({% post_url 2017-10-19-AX0 %}))

2. 求特解
    所有自由变量设为 0(这样最方便):

$$
\begin{equation}
\begin{cases} 
x_1 & + & 2x_3 & = & 1 \\
    &   & 2x_3 & = & 3 \\
\end{cases}
\end{equation}
$$

解得$
\begin{cases} 
x_1 & = & -2 \\\\ x_3 & = & \frac{3}{2}
\end{cases}
$

, 代入 $Ax=b$ 求得特解$
x_p=
\begin{bmatrix}
-2 \\\\ 0 \\\\ \frac{3}{2} \\\\ 0
\end{bmatrix}
$

然后将特解和零空间中任意向量任意向量加起来, 得到: $
x_{complete}=
\begin{bmatrix}
-2 \\\\ 0 \\\\ \frac{3}{2} \\\\ 0
\end{bmatrix}
+
c_1\begin{bmatrix}-2\\\\1\\\\0\\\\0\end{bmatrix}
+
c_2\begin{bmatrix}2\\\\0\\\\-2\\\\1\end{bmatrix}
$

## 秩
$m \times n$ 矩阵$A$, 秩为 $r \leq min(m, n)$

### 列满秩
即 $r=n<m$, 例如:

$$
\begin{equation}
A=
\begin{bmatrix}
1 & 3 \\
2 & 1 \\
6 & 1 \\
5 & 1 
\end{bmatrix}
\end{equation}
$$

消元后的形式为 $\begin{bmatrix} I \\ 0 \end{bmatrix}$, 没有自由列, 零空间中只有$0$向量. 此时的解要么有且只有一个, 要么就没有.

### 行满秩

即 $r=m<n$, 例如：

$$
\begin{equation}
A=
\begin{bmatrix}
1 & 2 & 6 & 5 \\
3 & 1 & 1 & 1 
\end{bmatrix}
\end{equation}
$$

消元后的形式为 $\begin{bmatrix} I & F \end{bmatrix}$. 这种情况下, 有自由变量, 所以解为无穷个.


### 行列满秩
$r=m=n$, 例如:

$$
\begin{equation}
A=
\begin{bmatrix}
1 & 2 \\
3 & 4 \\
\end{bmatrix}
\end{equation}
$$

$A$ 可以化简为 $R=I$，没有自由变量, 其零空间只包含 $0$ 向量, 方程有唯一解.

### 总结

$$\begin{array}{c|c|c|c}r=m=n&r=n\lt m&r=m\lt n&r\lt m,r\lt n\\R=I&R=\begin{bmatrix}I\\0\end{bmatrix}&R=\begin{bmatrix}I&F\end{bmatrix}&R=\begin{bmatrix}I&F\\0&0\end{bmatrix}\\1\ solution&0\ or\ 1\ solution&\infty\ solution&0\ or\ \infty\ solution\end{array}$$


```python

```
