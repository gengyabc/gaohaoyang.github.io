---
layout: post
title:  "线性代数 9 - 线性相关性、基、维数"
categories: 线性代数
tags:  数学
mathjax: true
author: Geng
---

* content
{:toc}
矩阵真实大小是多少呢? 一个 $m \times n$ 矩阵, 它的维数不一定是 $n$, 这和线性无关(独立)的列数有关. 我们将会看到列空间的真实维数是它的秩 $r$.

最终我们我懂得什么是**基**: 线性无关的向量**生成(张成)的空间**

空间中任意的向量都是基向量的一个特定的线性组合.

这里的概念是线性代数的核心所在.






## 线性无关(独立)

先从老朋友 $Ax=0$ 说起: 当 $Ax=0$ 唯一的解是 $x=0$ 时, $A$ 的列是线性独立的. 也就是没有非零解, 零空间只有 $0$.

那么一组向量 $ v_1, v_2, ..., v_n$ 是线性独立的话, 那么它们线性组合成 $0$ 的唯一可能性是: $ 0v_1 + 0v_2 + ... + 0v_n $

从秩的角度来说, $m \times n$ 矩阵:

* 线性无关: 秩为 $n$ , 没有自由变量, 零空间只有零向量
* 线性相关: 秩小于 $n$, 有 $n-r$, 零空间有无穷多个向量.

## 生成空间

列空间包含所有 $Ax$ 列的组合. 我们现在引入**生存空间**的概念: 列空间是由列**生成**的.

如果一个向量集合的线性组合可以填满整个空间, 那么它们**生成**这个空间.

## 基

一个向量不可能生成一个平面 $R^2$, 同样两个向量不能生成一个 $R^3$. 我们想要足够的线性独立的向量生成空间(不过不要多). **基**恰恰好.

$n \times n$ 单位矩阵的列就是常见的基, 叫做** $R^n$ 的标准基**

对于向量 $R^n$，如果 $n$ 个向量组成的矩阵为可逆矩阵，则这 $n$ 个向量为一组基

## 维数

这句话:

"对于向量 $R^n$，如果 $n$ 个向量组成的矩阵为可逆矩阵，则这 $n$ 个向量为一组基", 可以写为:

对于向量空间 $ R^n$，如果 $n$ 个向量组成的矩阵为可逆矩阵，则这 $n$ 个向量为该空间的一组基，而 $n$ 就是该空间的维数（dimension）.

回想零空间的概念, 它的维数就是自由变量的个数, 即: $n-r$
