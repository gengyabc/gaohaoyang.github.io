---
layout: post
title:  "图解线性代数 4 - 矩阵乘法"
categories: 线性代数
tags:  数学
mathjax: true
author: Geng
---

* content
{:toc}
你可能经常遇到需要一个变换接着一个变换地做, 应该怎么办呢?






## 复合变换

假设要先旋转, 然后剪切:

![]({{ site.url }}/assets/images/posts/machineLearning/图解线性代数/2017-10-14-matrix-transformation/1.gif)

这个新的变换显然不是旋转, 也不是剪切. 这种新的组合出来的线性变化, 叫做**复合变换**. 同样的, 这个复合变换也可以用 $\vec{i}$ 和 $\vec{j}$ 表示. 从上面图示可以看出, 这个新的变换可以写为矩阵 $\begin{bmatrix} 1 & -1 \\\\ 1 & 0\end{bmatrix}$

现在有个问题: 给你一个向量, 将它旋转后剪切, 你怎么做?

![]({{ site.url }}/assets/images/posts/machineLearning/图解线性代数/2017-10-14-matrix-transformation/2.gif)

根据图像提示, 我们可以先将它左乘旋转矩阵, 然后左乘剪切矩阵. 不过最后这个结果其实应该和直接将复合矩阵左乘效果一样:

![]({{ site.url }}/assets/images/posts/machineLearning/图解线性代数/2017-10-14-matrix-transformation/3.gif)

## 矩阵乘积
我们可以将这个新的复合矩阵成为最初两个矩阵的乘积:

![]({{ site.url }}/assets/images/posts/machineLearning/图解线性代数/2017-10-14-matrix-transformation/4.gif)

那么这个乘积怎么计算呢? 你应该已经能够猜到, 矩阵相乘, 就是一系列线性变换相机作用的过程. 有一点需要注意: 首先应用右侧矩阵的变换, 然后才是左边的:

![]({{ site.url }}/assets/images/posts/machineLearning/图解线性代数/2017-10-14-matrix-transformation/5.gif)


## 计算图解

假设矩阵 $ M_1 = \begin{bmatrix} 1 & -2 \\\\ 1 & 0\end{bmatrix}$, $ M_2 = \begin{bmatrix} 0 & 2 \\\\ 1 & 0\end{bmatrix}$, 他们分别变换效果与复合变换效果如下图, 那么我们可以不看动画求解吗?

![]({{ site.url }}/assets/images/posts/machineLearning/图解线性代数/2017-10-14-matrix-transformation/6.gif)

想先想下, $\vec{i}$ 去哪里了呢? 根据前面介绍, $\vec{i}$ 就是 $M_1$ 第一列 $(1, 1)$:

![]({{ site.url }}/assets/images/posts/machineLearning/图解线性代数/2017-10-14-matrix-transformation/7.gif)

$M_2$ 在 $(1, 1)$ 的作用, 就是 $M_2$ 与 $(1, 1)$ 相乘, 得到 $(2, 1)$, 这就是复合矩阵的第一列:

![]({{ site.url }}/assets/images/posts/machineLearning/图解线性代数/2017-10-14-matrix-transformation/8.gif)

同样方法, $\vec{j}$ 就是 $M_1$ 第二列 $(-2, 0)$, 与 $M_2$ 相乘后, 就是符合矩阵的第二列 $(0 , -2)$

![]({{ site.url }}/assets/images/posts/machineLearning/图解线性代数/2017-10-14-matrix-transformation/9.gif)

## 视频

[![]({{ site.url }}/assets/images/posts/machineLearning/图解线性代数/2017-10-14-matrix-transformation/10.png)](https://www.bilibili.com/video/av6128021/)
