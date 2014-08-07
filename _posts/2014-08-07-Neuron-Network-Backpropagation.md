---
layout: post
title: "Neuron Network Forward propagation"
category: ml
image: /image/neuron.jpg
---

###Cost function

假定我们有下面这个神经网络
<img src="/image/neuron-bp-1.jpg" width="100%"/>

样本为：
$$
{(x^{(1), y^{(1)}}),(x^{(2), y^{(2)}}),\dots,(x^{(m), y^{(m)}})}
$$
并且用L表示该神经网络的层数，用$s_l$表示第l层有几个节点（不含bias），用K表示输出层的节点个数。

针对K的个数不同，可以分为Binary分类和Multi-class 分类。

而神经网络的cost function值之前在logistic regression的泛化。
先回顾一下logistic regression cost function:

$$
J(\theta) = -\frac{1}{m}[\sum_{i=1}^my^{i}logh_{\theta}(x^{(i)}) + (1-y^{(i)})log(1-h_{\theta}(x^{(i)}))] + \frac{\lambda}{2m}\sum_{j=1}^n\theta^2_j
$$

神经网络的cost function则是把它扩展，输出层的K也要考虑到，因为有多个输出啊，而且每一层也有多个节点，都是前一层的输出。

$$
J(\theta) = -\frac{1}{m}[\sum_{i=1}^m\sum_{k=1}^Ky_k^{(i)}log(h_\theta(x^{(i)}))_k + (1-y_k^{(i)}log(1-(h\theta(x^{(i)}))_k)]+ \frac{\lambda}{2m}\sum_{l=1}^{L-1}\sum_{i=1}^{s_l}\sum_{j=1}^{s_l+1}(\Theta_{ji}^{(l)})^2
$$
