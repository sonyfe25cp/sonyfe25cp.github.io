---
layout: post
title: "Neuron Network Backpropagation"
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
J(\theta) = -\frac{1}{m}[\sum_{i=1}^m\sum_{k=1}^Ky_k^{(i)}log(h_\theta(x^{(i)}))_k + (1-y_k^{(i)}log(1-(h_\theta(x^{(i)}))_k)]+ \frac{\lambda}{2m}\sum_{l=1}^{L-1}\sum_{i=1}^{s_l}\sum_{j=1}^{s_l+1}(\Theta_{ji}^{(l)})^2
$$
注意，正则项依然是不要计算到bias项，i从1开始


###Gradient computation
针对文章开头的神经网络，假定我们只有一个样本(x, y), 于是我们FP的过程如下：
$$a^{(1)} = x$$
$$z^{(2)} = \Theta^{(1)}a^{(1)}$$
$$a^{(2)} = g(z^{(2)})$$,增加$$a_0^{(2)}$$
$$z^{(3)} = \Theta^{(2)}a^{(2)}$$
$$a^{(3)} = g(z^{(3)})$$,增加$$a_0^{(3)}$$
$$z^{(4)} = \Theta^{(3)}a^{(3)}$$
$$a^{(4)} = h_\Theta(x) = g(z^{(4)})$$

而该网络的BP过程如下：
损失函数是由预期值和激活值的实际误差产生。
$$\delta^{l} = 'error' of node j in layer l$$
于是：
$$\delta^{(4)} = a^{(4)} - y_j$$
对其他几层，则采用后一层的损失函数来继续计算

$$\delta^{(3)} = (\Theta^{(3)})^T\delta^{(4)}.*g'(z^{(3)}))$$

$$\delta^{(2)} = (\Theta^{(2)})^T\delta^{(3)}.*g'(z^{(2)})$$

技巧：$$g'(x) = x.*(1-x)$$这是因为g(x)是sigmod函数，才有的特性



