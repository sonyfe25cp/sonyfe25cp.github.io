---
layout: post
title: "Gauss Kernel"
category: ml
image: /image/gauss.png
---

###The Gauss Kernel

高斯核是用高斯(卡爾·弗里德里希·高斯)的名字来命名的, 他被认为是世界上最重要的数学家之一.

在1维，2维，和N维上的高斯核分别是：

$$
G_{1D}(x;\sigma)=-\frac{1}{\sqrt(2\pi)\sigma}e^{-\frac{x^2}{2\sigma^2}}
$$

$$
G_{2D}(x,y;\sigma)=\frac{1}{2\pi\sigma^2}e^{-\frac{x^2+y^2}{2\sigma^2}}
$$

$$
G_{ND}(\overrightarrow{x};\sigma)=-\frac{1}{(\sqrt{2\pi}\sigma)^N}e^{-\frac{|\overrightarrow{x}|^2}{2\sigma^2}}
$$

其中$\sigma$决定了高斯核的宽度。

在统计学中，当我们在高斯概率密度函数中讨论$\sigma$时，它叫做标准差，而$\sigma^2$就是方差。

从现在开始，我们将高斯核作为一种孔径函数(aperture function)的观察，而$\sigma$就是内径尺度(inner scale),并且只考虑$\sigma > 0$的情况。

### Normalization

在1维高斯核前面的$\frac{1}{\sqrt{2\pi}\sigma}$就是正则化常量。

原因是，对后面的项积分的时候会剩余这些：
$$
\int_{-\infty}^{\infty}e^{-x^2/2\sigma^2}dx=\sqrt{2\pi}\sigma
$$

有了这部分正则化常量，高斯核就是一个正则化核函数：它的积分对于任意$\sgima$在整个空间都是一致的。也就是说，通过增加$\sigma$可以大幅度降低振幅。（本来就是这样，$\sigma$关系到高斯图象的宽度。）

正则化确保了当我们用高斯核来模糊图象的时候，图片的平均灰度保持一致，也就是平均灰度水平不变性。

###Cascade property

不管$\sigma$的情况，高斯核的形状保持不变。例如：当我们用两个高斯核来相加得到一个新的高斯核。
$$g_{new}(\overrightarrow{x};\sigma_1^2+\sigma_2^2) = g_1(\overrightarrow{x};\sigma_1^2)g_2(\overrightarrow{x};\sigma_2^2)$$

这种新构成的函数跟原函数相似的现象叫做自相似性。高斯核就是一个具有自相似性的函数。

###The scale parameter

为了避免求和平方项，通常将$2\sigma^2\to t$， 于是高斯核函数得到了一个简单的形式。在N维情况下：
$$
G_{ND}(\overrightarrow{x}, t)=-\frac{1}{(\pi\iota)^{N/2}}e^{-frac{x^2}{t}}
$$

用t可以代替$$\frac{\partial L}{\partial t}=\frac{\partial^2L}{\partial x^2}+\frac{\partial^2L}{\partial y^2}+\frac{\partial^2L}{\partial z^2}$$

t通常叫做方差variance.

为了将高斯核的自相似性更清晰，我们引入一个新的维度无关的空间向量$\widetilde{x}=\frac{x}{\sigma\sqrt{2}}$,也就是我们重新参数化了x轴。

现在的高斯核函数变成了$$g_n(\widetilde{x};\sigma)=\frac{1}{\sigma\sqrt{2\pi}}e^{-\widetilde{x}^2}$$



###Reference

1. www.stat.wisc.edu/~mchung/teaching/MIA/reading/diffusion.gaussian.kernel.pdf.pdf
