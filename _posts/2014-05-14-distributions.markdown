---
layout: post
title: "回顾几个简单的概率分布"
date: 2014-05-14 17:26:02
category: math
---

###背景

看LDA的时候发现自己对几个常见的分布，总是云里雾里，但是这东西又是必须要搞明白的，现在统一总结一下。

###Binary Variable

	以硬币为例，正面为1，反面为0。
	
假定这是一个坏掉的硬币（它不均匀），假设它正面的概率是: 
$p(x=1|\mu)=\mu$，其中 $0 \le \mu \le 1$,对于反面来说 $p(x=0|\mu)=1-\mu$。

这里采用伯努利分布可以写成：

$$
Bern(x|\mu) = \mu^x(1-\mu)^{1-x}
$$

它的期望是： $$E[x]=\mu$$，方差是: $$var[x]=\mu(1-\mu)$$

现在假定我们有一个$x$的观察数据集 $$D=\{x_1,\dots,x_N\}$$，我们来构造一个基于$\mu$似然函数，这些$x$都是独立的从该函数上取得。于是：

$$
p(D|\mu)=\prod_{n=1}^Np(x_n|\mu)=\prod_{n=1}^N\mu^{x_n}(1-\mu)^{1-x_n}
$$

由于$\prod$不便于计算，为了方便计算最大似然估计，采用计算$log$似然函数的方式：

$$
lnP(D|\mu)=\sum_{n=1}^Nlnp(x_n|\mu)=\sum_{n=1}^N\{x_nln\mu+(1-x_n)ln(1-\mu)\}
$$

对$\mu$求导，使其等于0，解出$\mu$的值，为当前似然估计的最大值点：

$$\mu_{ML}=\frac{1}{N}\sum_{n=1}^Nx_n$$

这也就是样本均值。

####过拟合

假定我们扔了三次硬币，而这三次恰好都是正面朝上，于是由最大似然估计可以得出，$$N=m=3, \mu_{ML}=1$$， 这是与常识所不符合的。也就是说我们的模型过拟合了。

下面通过引入先验概率来得到一个更靠谱点的结果。

###Binomial Distribution(二项分布)

$$
Bin(m|N,\mu)=\binom{N}{m}\mu^m(1-\mu)^{N-m}
$$

where

$$
\binom{N}{m} \equiv \frac{N!}{(N-m)!m!}
$$

###Beta Distribution(Beta分布)

从前面的二项分布和伯努利分布可以看出，直接采用观察到的3次正面来计算，会导致过拟合，于是需要引入先验概率$$p(\mu)$$。由于我们要计算$$p(x)$$，而通过贝叶斯定律$$p(x)=p(x|\mu)*p(\mu)$$，于是我们发现，如果$p(\mu)$跟$$p(x|\mu)$$的形式一样，那么计算起来就简单了，于是引出了共轭的概念。

####共轭

如果选择一个先验概率是$$\mu$$的幂次与$$1-\mu$$的幂次的乘积，而后验概率分布也是这个函数形式，那么这种特性叫做共轭。（先验概率公式与后验概率公式的形式一样。）

这里我们采用Beta分布作为这个先验概率：

$$
Beta(\mu|a,b)=\frac{\Gamma(a+b)}{\Gamma(a)\Gamma(b)}\mu^{a-1}(1-\mu)^{b-1}
$$
其中，$$\Gamma(x)$$是Gamma函数。

Beta分布的期望: $$E[\mu]=\frac{a}{a+b}$$
方差：$$var[\mu]=\frac{ab}{(a+b)^2(a+b+1)}$$

其中，a，b经常被称作超参数(Hyperparameters)，因为他们控制$\mu$的分布。

###Multinomial Variables

相对于二项变量，当随机变量可以取1到K之间的元素时，这就是多项分布了。（从扔一个硬币变成扔一个色子。）

例如：$$x=(0,0,1,0,0,0)^T$$

如果我们通过参数$\mu_k$来描述$$x_k=1$$的分布，那么这个分布就是：
$$p(x|\mu)=\prod_{k=1}^{K}\mu_k^{x_k}$$
,其中
$$\mu=(\mu_1,\mu_2,\dots,\mu_k)^T$$
,并且满足
$$\mu_k > 0$$
和
$$\sum_k\mu_k=1$$。

###Dirichlet Distrbution

The normalized form:

$$
Dir(\mu|\alpha)=\frac{\Gamma(\alpha_0)}{\Gamma(\alpha_1) \dots \Gamma(\alpha-K)}\Pi_{k=1}^K\mu_k^{\alpha_k-1}
$$

其中，$$\Gamma(x)$$ 是伽马函数，而
$$
\alpha_0=\sum^K_{k=1}\alpha_k
$$

用先验概率*似然函数，可以得到$$\{\mu_k\}$$的后验分布形式：
$$
p(\mu|D,\alpha) \propto p(D|\mu)p(\mu|\alpha)\propto \Pi_{k=1}^K\mu_k^{\alpha_k+m_k-1}
$$

还是不太懂这部分，进一步理解之后补上。




















