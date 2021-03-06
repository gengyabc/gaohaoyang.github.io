---
layout: post
title:  "概率论 10. 马尔科夫链的性质"
categories: 概率论
tags:  数学
author: Geng
---

这部分内容大量使用**迭代**思想.

**迭代**基本上就是一种数学上的推卸责任, 然后出一个方程组, 然后就算出来了. 







## 马尔科夫链的长期性质
马尔科夫链模型中, 我们常常关心他的长期状态性质, 关心它收敛的性质. 

如果一个马尔科夫链有稳态, 那么对于一个状态 *j*, 处于它的概率 \\(r_{ij}(n)\\) 随着 *n* 的增大会趋近于一个独立于初始状态 *i* 的极限值, 记为 \\(\pi_j\\):

$$ \pi_j \sim P(X_n = j), \ n很大时 $$

这个 \\(\pi_j\\) 就是状态 *j* 的**稳态概率**.


### 稳态收敛定理

#### 周期马尔科夫链
首先想象一个周期马尔科夫链, 它有稳态吗? 对于一个周期链, 每步变化都要变为另外一个子集而不是稳定下来, 也就是就算 *n* 足够大, 也不能稳定在一个值. 显然**周期链不具有稳态**.

#### 多个长返类的马尔科夫链
然后再考虑有多个长返类的马尔科夫链, 有稳态吗? 回答这个问题, 首先问自己两个问题, 最终状态在哪个长返类? 和初始状态有关系吗? 显然我不知道最终状态在哪个长返类, 而且随着初始状态的不同, 最终在哪里的概率也不相同, 所以**有多个长返类的马尔科夫链也没有稳态性质**. 

#### 非周期, 单个长返类的马尔科夫链
那么我们就考虑非周期, 单个长返类的马尔科夫链. 当 *n* 很大时, 有:

$$ \pi_j = P(X_n = j), 当n无穷大时 $$

那么, 假设现在处于 *n-1* 步, 状态k, 显然也有:

$$ \pi_k = P(X_{n - 1} = k), 当n无穷大时 $$



如果现在我要从 *n-1*步 到 *n*步, 从状态 *k* 到状态 *j* , 所有可能的走法可以写为:

$$ \pi_j = \sum_{k=1}^{m} \pi_kp_{kj}  $$

而且所有 \\(p_k\\)相加和为 *1* . 

![]({{ site.url }}/assets/images/posts/machineLearning/概率论/2017-06-17-properties-of-markov-chains\steady.png)

#### 长期频率解释
因为是稳态, 而且和初始状态无关, 那么如果我不知道怎么计算的话, 可以使用暴力计算. 随便设置一个初值, 让计算机计算很大的步数 *n* , 计算某个状态经历的次数 *k* , 那么稳态概率可以看做: \\(k/n\\).

#### 生灭过程
这周马尔科夫链状态转移只发生发相邻状态之间, 或者状态保持不变.

![]({{ site.url }}/assets/images/posts/machineLearning/概率论/2017-06-17-properties-of-markov-chains\bd.png)

对于这种过程, 关键就是注意到**局部平衡**

## 马尔科夫链的短期性质
对于开始于暂态的过程, 我们对首次到达常返态的分布和对应到达时间感兴趣. 对于暂态来说, 常返态是**吸收的**. 

### 吸收概率

假设一个马尔科夫链, 它有一个吸收状态 *s*, 对于从状态 *i* 开始, 最终到达 *s* 的概率 \\(a_i\\). 我不知道怎么算, 那么我问问我的下一步状态, 它的短期性质是什么, 综合它们的所有信息:

$$ a_s = 1 $$

$$ a_i = 0, \ 不是\ s\ 的所有吸收态 $$

$$ a_i = \sum_{j=1}^m p_{ij}a_j, \ 对于所有非常返态\ i $$

### 平均吸收时间
又是迭代了, 我不知道这个时间是多少, 所以我我问问我的下一步, 它的平均吸收时间. 那么我的平均时间就是下一步的平均时间 +1:

$$ \mu_i = 0, \ i\ 是常返态 $$

$$ \mu_i = 1 + \sum_{j=1}^m p_{ij}\mu_j, \ i\ 是暂态 $$

### 平均首访时间和回访时间
和平均吸收时间类似, 都是计算到达某个状态的时间. 没必要记那么多公式, 用的时候自己想想就出来了, 不再说明. 

## 连续马尔科夫链
我们可以用离散马尔科夫链近似连续的, 只要将时间间隔设置到足够小即可. 那么大多数的问题在一个小区间 \\(\delta\\) 计算即可.
