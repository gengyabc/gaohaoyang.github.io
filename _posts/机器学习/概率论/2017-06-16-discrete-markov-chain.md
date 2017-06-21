---
layout: post
title:  "概率论 9. 离散马尔科夫链"
categories: 概率论
tags:  理论
author: Geng
---

* content
{:toc}


与之前所述的伯努利过程和泊松过程不同, 马尔科夫过程是有记忆的. 不过马尔科夫过程记忆力也没有特别好: 未来的**状态**只与现在状态有关系, 而与过去的状态无关. 

## 离散马尔科夫链简介
我们可以把它看成这个公式:

$$ 未来状态 = f(现在状态, 噪声) $$

也就是说, 在考虑到噪声影响下, 未来状态部分程度上取决于现在状态. 






我们假设所有可能的状态组成一个**状态空间**: \\(S, \ S = {1, 2, ... , m}\\). 随着时间的推移,状态转换, 也就是空间转换. 那么, 从**旧空间**转移到**新空间**的概率为:

$$ P(新空间|旧空间) $$

因为这是一个空间变换的过程, 所以上面这个公式也称作**转移概率**: \\(p_{旧新}\\). 更正式地:

$$ p_{ij} = P(X_{n+1} = j | X_n = i) $$

即从 \\(i\\) 状态转换到 \\(j\\) 状态的概率. 可见, 这其实就是我们之前接触过的**条件概率**问题. 因为任意时间 *n* 的下一个状态 *n+1* 只与 *n* 时的状态有关, 所以:

$$ P(X_{n+1} = j | X_n = i) = P(X_{n+1} = j | X_n = i,  X_{n-1} = i_{n-1}, ..., X_0=i_0)  $$ 

根据全概率原理, 我们可以得到:

$$ \sum_{i=1}^{m} p_{ij}=1 $$

### 描述马尔科夫链
可以用**转移概率矩阵**描述:

$$  
\begin{bmatrix}
    p_{11}       & p_{12} & p_{13} & \dots & p_{1m} \\
    p_{21}       & p_{22} & p_{23} & \dots & p_{2m} \\
    \vdots       & \vdots & \vdots & \vdots & \vdots  \\
    p_{m1}       & p_{m2} & p_{m3} & \dots & p_{mm}
\end{bmatrix}
$$

也可以用**转移概率图**描述:

![]({{ site.url }}/assets/images/posts/machineLearning/概率论/2017-06-16-discrete-markov-chain\transform_graph.png)

## n步转移概率
上面介绍的是经过一步, 就能从旧空间转移到新空间. 那么, 如果需要多步, 就是n步转移的问题了:

$$ r_{ij}(n) = P(X_{n+1} = j | X_{0} = i) $$

这个问题可以由迭代公式, * Chapman-Kolmogorov (C-K)*公式计算:

$$ r_{ij}(n) = \sum_{k=1}^{m} r_{ik}(n-1)p_{kj}  $$

它可以由全概率公式证明, 但是下图更加直观:

![]({{ site.url }}/assets/images/posts/machineLearning/概率论/2017-06-16-discrete-markov-chain\ck.png)

我们通过不同路径, 走了 *n-1* 步, 从 *i* 走到了 *n-1* , 最后我们只需要计算在第 *n-1* 这一步所有的可能性到达 *j* 的全概率即可.

在 *n* 足够大的时候, 很多情况下我们会到达一个**稳态**, 此时不管初始条件如何, 到达每一个状态的概率是恒定的. 

## 状态分类

### 可达的
在转移状态图上, 如果可以画出一条从状态 *j* 到状态 *i* 的路径, 那么状态 *j* 到状态 *i* 是**可达的(accessible)**:

![]({{ site.url }}/assets/images/posts/machineLearning/概率论/2017-06-16-discrete-markov-chain\accessible.png)

### 常返类
\\(A(i)\\)为所有从状态 *i* 可达的集合, 组成一个常返类. \\(A(i)\\)之外的状态不是从\\(A(i)\\)之内的状态可达的.

### 常返的(recurrent)和暂时的(transient)

从 *i* 到了 *j*, 又能从 *j* 回到 *i*, 那么状态 *i* 就是常返的. 如果是非常返的, 那么就是暂时的. 

![]({{ site.url }}/assets/images/posts/machineLearning/概率论/2017-06-16-discrete-markov-chain\state.png)

### 周期性
一个常返类有若干不想交的子集, 每次转换都从一个子集依次转移到下一个子集, 那么这个常返类就是周期的, 比如:

![]({{ site.url }}/assets/images/posts/machineLearning/概率论/2017-06-16-discrete-markov-chain\periodic.png)




