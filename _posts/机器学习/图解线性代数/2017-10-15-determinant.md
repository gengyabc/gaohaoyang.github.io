---
layout: post
title:  "图解线性代数 5 - 行列式"
categories: 线性代数
tags:  数学
mathjax: true
author: Geng
---

* content
{:toc}

> 计算的目的不在于数字本身, 而在于洞察其背后的意义 -- 理查德 哈明

## 量化伸缩量
之前的很多线性变换, 有些进行了拉伸, 有些进行了压缩, 那么如何测量究竟对空间进行了多少伸缩呢?






![]({{ site.url }}/assets/images/posts/machineLearning/图解线性代数/2017-10-15-determinant/1.gif)

假设一个矩阵 $\begin{bmatrix}3 & 0 \\\\ 0 & 2 \end{bmatrix}$, 我们可以将它看做将 $\vec{i}$ 伸长了 3 倍, $\vec{i}$ 伸长了 2 倍:

![]({{ site.url }}/assets/images/posts/machineLearning/图解线性代数/2017-10-15-determinant/2.gif)

现在, 我们关注 $\vec{i}$ 和 $\vec{j}$ 围成的方形, 变化之后, 他就变成了一个 $2 \times 3$ 的矩形, 这个时候我们可以认为线性变换就它的面积变为之前的 6 倍:

![]({{ site.url }}/assets/images/posts/machineLearning/图解线性代数/2017-10-15-determinant/3.gif)

类似的, 一个剪切矩阵 $\begin{bmatrix}1 & 1 \\\\ 0 & 1 \end{bmatrix}$, 也就是 $\vec{i}$ 不变, $\vec{j}$ 变为了 $(1, 1)$. 那么 $\vec{i}$ 和 $\vec{j}$ 围成的方形变换后变成了一个平行四边形, 不过面积仍然为 1, 看起来线性变换也不一定会更改面积哈:

![]({{ site.url }}/assets/images/posts/machineLearning/图解线性代数/2017-10-15-determinant/4.gif)

实际上, 只要你知道单位正方形面积变化的比例, 它就能告诉你其他任意区域的面积变化比例. 需要注意的是, 无论一个方格如何变化, 其他大小的方格或者形状都会有同样的变化.

## 行列式

上面所说的这个缩放比例, 就是**行列式**.

![]({{ site.url }}/assets/images/posts/machineLearning/图解线性代数/2017-10-15-determinant/5.gif)

再看几个例子, 比如行列式为 3 和 1/2:

![]({{ site.url }}/assets/images/posts/machineLearning/图解线性代数/2017-10-15-determinant/6.gif)

对了, 还有一个降维打击的, 二维变成一维的:

![]({{ site.url }}/assets/images/posts/machineLearning/图解线性代数/2017-10-15-determinant/7.gif)

最后这个降维打击说明, 只要检查一个矩阵的行列式是否为 0, 就能知道这个矩阵的变换是否将空间降维(三体迷们, 你们看书的时候写公式了吗?).

这种情况下, 其实就是矩阵的列是线性相关的.

## 行列式符号
不过, 行列式是可以出现负值的, 面积怎么可能回事负数呢? 

符号和方向有关系. 比如, 注意这个变换将整个平面翻转了, 就像翻了一页纸:

![]({{ site.url }}/assets/images/posts/machineLearning/图解线性代数/2017-10-15-determinant/8.gif)

从 $\vec{i}$ 和 $\vec{j}$ 的角度考虑. 开始, $\vec{i}$ 逆时针转到 $\vec{j}$, 如果变换后, $\vec{i}$ 相反方向转到 $\vec{j}$, 那么空间就翻转了. 

![]({{ site.url }}/assets/images/posts/machineLearning/图解线性代数/2017-10-15-determinant/9.gif)

空间翻转了, 行列式就会变负值, 其绝对值仍然是缩放比例:

![]({{ site.url }}/assets/images/posts/machineLearning/图解线性代数/2017-10-15-determinant/10.gif)

为什么负号与翻转有关呢, 看看下面这从正到负的动图就清楚了:


![]({{ site.url }}/assets/images/posts/machineLearning/图解线性代数/2017-10-15-determinant/11.gif)

## 三维空间

三维空间, 就是空间的缩放了. 那么负号呢? 怎么来的?

三维空间中, 用的是**右手定则**: 右手大拇指, 食指和中指分别指向 $\vec{i}$, $\vec{j}$, $\vec{k}$(具体那个指头对哪个向量不重要, 重要的是顺序不能变). 变换之后如果还可以使用右手定则, 那么行列式为正. 如果不能使用右手定则而是左手定则, 就为负数了:

![]({{ site.url }}/assets/images/posts/machineLearning/图解线性代数/2017-10-15-determinant/12.gif)

## 计算方法

那么, 行列式的值为什么是我们学校里学的那样计算呢?

下面动图中, $3$ 是 $x$ 方向伸缩比例, $2$ 是 $y$ 方向伸缩比例, 因为其他项都是 0, 那么 $6$ 给出的就是单位正方形伸缩后的矩形面积.

![]({{ site.url }}/assets/images/posts/machineLearning/图解线性代数/2017-10-15-determinant/13.gif)

如果如下图, $b$ 不是 0, 那么最后得到一个平行四边形, 面积仍是 $ad$:

![]({{ site.url }}/assets/images/posts/machineLearning/图解线性代数/2017-10-15-determinant/14.png)

粗略的说, 如果 $b$ 和 $c$ 都不是 $0$, $bc$ 告诉你这个图形在对角线方向伸缩多少:

![]({{ site.url }}/assets/images/posts/machineLearning/图解线性代数/2017-10-15-determinant/15.gif)

## 视频

[![]({{ site.url }}/assets/images/posts/machineLearning/图解线性代数/2017-10-15-determinant/16.png)](https://www.bilibili.com/video/av6179111/)


