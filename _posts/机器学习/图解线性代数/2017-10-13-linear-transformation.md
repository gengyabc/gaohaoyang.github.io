---
layout: post
title:  "图解线性代数 3 - 线性变换和矩阵"
categories: 线性代数
tags:  数学
mathjax: true
author: Geng
---

* content
{:toc}

> 不幸的是, 没人都告诉你矩阵是什么. 你得自己去看看.  -- 墨菲斯 (其实我不知道这是谁)

这个视频是作者最喜欢的一个, 也是最重要的一个, 所以我也多做些笔记.





## 线性变换

变换可以认为就是**输入**通过一个方程的作用, 变成了相应的**输出**. 线性代数中, 就是一个输入向量变成了一个输出向量, 如下图所示:

![]({{ site.url }}/assets/images/posts/machineLearning/图解线性代数/2017-10-13-linear-transformation/1.gif)

空间中所有的点, 都按照这个方程变换的话, 就会都移动到对应的位置:

![]({{ site.url }}/assets/images/posts/machineLearning/图解线性代数/2017-10-13-linear-transformation/2.gif)

更直观的方法是将空间划分为网格, 然后观察这些网格的变化(背景网格是变换前网格):

![]({{ site.url }}/assets/images/posts/machineLearning/图解线性代数/2017-10-13-linear-transformation/3.gif)

变换是很神奇的, 会有各种各样的变换:

![]({{ site.url }}/assets/images/posts/machineLearning/图解线性代数/2017-10-13-linear-transformation/4.gif)

我们所说的**线性变换**, 是一种特殊的变换:
1. 直线在变换后仍然保持为直线.
2. 原点保持固定.

![]({{ site.url }}/assets/images/posts/machineLearning/图解线性代数/2017-10-13-linear-transformation/5.png)

比如下面这个, 就不满足直线在变换后仍然保持为直线:

![]({{ site.url }}/assets/images/posts/machineLearning/图解线性代数/2017-10-13-linear-transformation/6.gif)

下面这个不满足原点保持固定:

![]({{ site.url }}/assets/images/posts/machineLearning/图解线性代数/2017-10-13-linear-transformation/7.gif)

下面这个这个很有迷惑性, 看起来好像是保持直线的, 而且原点没有变. 但是这是因为只显示了水平和垂直线, 如果你看对角线, 就发现明显不是线性变换了:

![]({{ site.url }}/assets/images/posts/machineLearning/图解线性代数/2017-10-13-linear-transformation/8.gif)

一般来说, 你应该把线性变换看作"保持网格线平行且等距". 

线性代数的任务之一, 就是怎样数字描述这些变换呢?

## 记录线性变换

我们只需要记下基向量 $\vec{i}$ 和 $\vec{j}$ 变化前后的位置, 所有向量都会随之而变. 

比如一个向量 $\vec{v} = (-1, 2)$, 可以写成 $\vec{v} = -1\vec{i} + 2\vec{j}$, 经过线性变换后, 不管怎么变, 还满足 $\vec{v} = -1\vec{i} + 2\vec{j}$:

![]({{ site.url }}/assets/images/posts/machineLearning/图解线性代数/2017-10-13-linear-transformation/9.gif)

所以, 我们只要知道了 $\vec{i}$ 和 $\vec{j}$ 是怎样变的, 就知道其他任何向量将会怎么变. 比如上面的例子, 我们从图上可以看出, $\vec{i}$ 和 $\vec{j}$ 变成了 $(1, -2)$ 和 $(3, 0)$, 那么根据 $\vec{v} = -1\vec{i} + 2\vec{j}$ 可以计算出, $\vec{v} = -1\begin{bmatrix}1 \\\\ -2\end{bmatrix} + 2\begin{bmatrix}3 \\\\0\end{bmatrix} = \begin{bmatrix}5 \\\\2\end{bmatrix} $, 和图上对应的左边相同. 

![]({{ site.url }}/assets/images/posts/machineLearning/图解线性代数/2017-10-13-linear-transformation/10.gif)

## 矩阵

上面例子可以看出, 二维空间中, 一个线性变换可以由四个数字 ($\vec{i}$ 和 $\vec{j}$的坐标) 完全确定. 我们可以将这两个向量包装起来, 形成一个 $2 \times 2$ 矩阵, 它的列就是对应的 $\vec{i}$ 和 $\vec{j}$ (第一列对应 $\vec{i}$, 第二列对应 $\vec{j}$:

![]({{ site.url }}/assets/images/posts/machineLearning/图解线性代数/2017-10-13-linear-transformation/11.gif)

如果你想知道线性变换对某个向量的作用, 那就取出向量的坐标, 将它们分别与矩阵对应的列相乘, 然后将结果加起来就行了

![]({{ site.url }}/assets/images/posts/machineLearning/图解线性代数/2017-10-13-linear-transformation/12.gif)

所以, 任何一组基向量都是描述线性变换的信息. 

这个过程可以看做是矩阵和向量的乘法. 比如矩阵 $A = \begin{bmatrix}a & b \\\\c & d \end{bmatrix} $, 向量 $\vec{v} =\begin{bmatrix} x \\\\y \end{bmatrix}$, 向量 $\vec{v}$ 在 $A$ 描述的空间中的坐标可以看成如下图所示的乘法:

![]({{ site.url }}/assets/images/posts/machineLearning/图解线性代数/2017-10-13-linear-transformation/13.gif)

可见, 我们可以把矩阵的列看成是变换后的基向量, 把矩阵和向量的乘法看成它们的线性组合.

## 线性变换举例

### 逆时针旋转90度

这种情况下, $\vec{i}$ 变成了 $(0, 1)$, $\vec{j}$ 变成了 $(-1, 0)$, 那么矩阵就成了 $\begin{bmatrix}0 & -1 \\\\1 & 0 \end{bmatrix} $. 想要计算任何向量旋转 90 度后的效果, 只要把它们相乘即可. 

![]({{ site.url }}/assets/images/posts/machineLearning/图解线性代数/2017-10-13-linear-transformation/14.gif)

### 剪切
这种情况下, $\vec{i}$ 不变, $\vec{j}$ 变成了 $(1, 1)$, 那么矩阵就成了 $\begin{bmatrix}1 & 1 \\\\0 & 1 \end{bmatrix} $. 想要计算任何向量旋转剪切后的效果, 只要把它们相乘即可:

![]({{ site.url }}/assets/images/posts/machineLearning/图解线性代数/2017-10-13-linear-transformation/15.gif)

## 总结

矩阵和向量的乘法就是**矩阵所指定的线性变换作用于一个向量**, 以后不管看到什么矩阵, 你都把它看做是一种空间的变换, 那么可能你对线性代数的理解就会更上一层楼了. 

![]({{ site.url }}/assets/images/posts/machineLearning/图解线性代数/2017-10-13-linear-transformation/16.gif)

## 视频

[![]({{ site.url }}/assets/images/posts/machineLearning/图解线性代数/2017-10-13-linear-transformation/17.png)](https://www.bilibili.com/video/av6043439/)


