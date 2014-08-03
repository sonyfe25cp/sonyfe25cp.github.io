---
layout: post
title: "Neuron Network Forward propagation"
category: ml
image: /image/neuron.jpg
---

###Neuron Network Fp

<img src="/image/neuron-network.jpg" width="100%"/>

在前一节的model部分已经知道下面的式子：

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
h_{\Theta}(x) = a_1^{(3)} = g(\Theta_{10}^{(2)}a_0^{(2)} + \Theta_{11}^{(2)}a_1^{(2)} +\Theta_{12}^{(2)}a_2^{(2)} +\Theta_{13}^{(2)}a_3^{(2)})
$$

定义:
$$z_1^{(2)} = \Theta_{10}^{(1)}x_0 + \Theta_{11}^{(1)}x_1 +\Theta_{12}^{(1)}x_2 +\Theta_{13}^{(1)}x_3$$

于是$$a_1^{(2)}=g(z_1^{(2)})$$

而
$$
x = \left[ \begin{array}{c} 
x_0\\ 
x_1\\
x_2\\
x_3\end{array}\right]
$$
$$
z^{(2)} = \left[ \begin{array}{c} 
z_1^{(2)}\\
z_2^{(2)}\\
z_3^{(2)}\end{array}\right]
$$

可以得到：
$$
h_\Theta(x) = g(\Theta_{10}^{(2)}a_0^{(2)} + \Theta_{11}^{2}a_1^{(2)} + \Theta_{12}^{(2)}a_2^{(2)} + \Theta_{13}^{(2)}a_3^{(2)})
$$
