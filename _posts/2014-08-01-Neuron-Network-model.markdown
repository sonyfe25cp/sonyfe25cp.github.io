---
layout: post
title: "Neuron Network Model"
category: ml
image: /image/neuron.jpg
---

###Logistic unit

先看一下简单的logistic unit，如下图：

<img src="/image/logitic-unit.jpg" width="100%">

其中 x1, x2, x3 是输入单元，$$h_{\theta}(x)$$是输出单元，如果我们采用logistic(sigmod)函数来计算它，那么他就是一个logistic unit了。

$$
h_\theta(x)=\frac{1}{1+e^{-\theta^Tx}}
$$

通常我们会给输入单元增加一个$$x_0$$节点，用它来解决bias问题，也就是通常说的bias unit。

这样，输入单元就是$$[x_0, x_1, x_2, x_3]^T$$，对应的权重也就是$$[\theta_0, \theta_1, \theta_2, \theta_3]^T$$.

###Neuron Network

<img src="/image/neuron-network.jpg" width="100%"/>

上图是通常说的神经网络结构，Layer1是输入层，Layer3是输出层，Layer2是隐含层。

定义：$$a_1^{(2)}$$中上标表示第2层，下标表示第1个节点。

由于通常我们都增加一个bias unit,于是，真实的节点图如下：
<img src="/image/neuron-network2.jpg" width="100%"/>

定义：

	1. $$a_i^{(j)}$$ 表示第j层的第i个节点的激活值(前一层的输入经过激活函数计算之后到达该节点的值)
	2. $$\Theta^{(j)}$$ 表示从第j层到第j+1层的权重矩阵

于是：

$$
a_1^{(2)} = g(\Theta_{10}^{(1)}x_0 + \Theta_{11}^{(1)}x_1 +\Theta_{12}^{(1)}x_2 +\Theta_{13}^{(1)}x_3)
$$

$$
a_2^{(2)} = g(\Theta_{20}^{(1)}x_0 + \Theta_{21}^{(1)}x_1 +\Theta_{22}^{(1)}x_2 +\Theta_{23}^{(1)}x_3)
$$

$$
a_3^{(2)} = g(\Theta_{30}^{(1)}x_0 + \Theta_{31}^{(1)}x_1 +\Theta_{32}^{(1)}x_2 +\Theta_{33}^{(1)}x_3)
$$

$$
h_{\Theta}(x) = a_1^{(3)} = g(\Theta_{10}^{(2)}a_0^{(2)} + \Theta_{11}^{(2)}a_0^{(2)} +\Theta_{12}^{(2)}a_0^{(2)} +\Theta_{13}^{(2)}a_0^{(2)})
$$

于是，如果在第j层有$$s_j$$个节点，并且j+1层有$$s_{j+1}$$个节点，那么$$\Theta^{(j)}$$就是$$s_{j+1}*(s_j+1)$$的矩阵。

