---
layout: post
title:  "概率论 2. 排列组合"
categories: 概率论
tags:  理论
author: Geng
---

* content
{:toc}


按理说，排列组合是不应该单拿出来的，但是因为我自己对这方便怎么都搞不清，所以单独拿出来，以备以后时长更新。

排列组合就是一个数数的过程，数一数到底有多少种结果，是在**等可能概率型**中的概念。






## 排列
假设我们正在进行一组试验，如下图所示：

![]({{ site.url }}/assets/images/posts/machineLearning/概率论/2017-05-21-permulation-combination/tree.png)

我们可以想象沿着上图的各个分支游走，各个阶段的可能个数分别为n1, n2, n3。那么最后可能的结果就有\\(n1 \* n2 \* n3\\)个。

可以注意到，这个时候的结果因为我们沿着不同分支走，有顺序关系，所以是一个**排列**。

### n选k
下面分析一下从n个物体中拿出k个物体，有几种拿法。因为是几种拿法，那么肯定是和顺序相关的（如果是可以出来几种结果组合，那么就和顺序无关了）。

第一次，从n中选一个；第二次，从(n - 1)中选一个；第三次，从(n - 2)中选一个；一直重复，直到第k次，从(n - (k - 1))中选一个。那么根据上面讨论，一共有\\(n \* (n - 1) \* (n - 2) \*...\*(n - k + 1)\\)中情况，或者写为：

$$ \begin{align*} &  (n * (n - 1) * (n - 2) *...*(n - k + 1) \\&
= \dfrac {(n * (n - 1) * (n - 2) *...*2 * 1} {(n-k) * ... * 2*1} \\& 
= \dfrac {n!} {(n-k)!} \\&
\end{align*}
$$

## 组合
组合就是看做n个物体中，挑出来k个物体，有几种可能的组合。记为：\\( \left( \begin{matrix} n \newline k\end{matrix} \right) \\)。

那么如何计算\\( \left( \begin{matrix} n \newline k\end{matrix} \right) \\)呢？我们可以观察下图：

![]({{ site.url }}/assets/images/posts/machineLearning/概率论/2017-05-21-permulation-combination/relation.png)

可见，n挑出k个的组合，可以从另一条路径去理解：首先我们可以看到，n选k排列的话，有\\(\dfrac {n!} {(n-k)!}\\)种排列方法，那么其中有多少重复的组合呢？看一下另一个路径：“k个数排队”，有k！个排列方法。也就是说，\\(\dfrac {n!} {(n-k)!}\\)中有k！个重复的组合。那么：
$$ \left( \begin{matrix} n\\ k\end{matrix} \right) = \dfrac {n!} {k! (n-k)!} $$
