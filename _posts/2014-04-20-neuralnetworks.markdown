---
layout: post
title: "Hopfield Neural Network的读书笔记之一"
date: 2014-04-20 11:57:50
categories: NeuralNetWork Hopfield
---

###什么是Hopfield Neural Network

Hopfield Neural Network是Neural Network中最基本的一中。

###特点

* 节点全连接
    
    该NN的所有节点都是全连接的，但是与自身没有连接。

* 自反性(Autoassociative)

    自反性是指如果该NN可以识别一个pattern，那么它就会返回这个pattern，当输入是它认识的pattern。

###例子

下面通过一个4个节点的Hopfield NeuralNetWork 来说明它是如何训练和使用。

(TODO:补一个全连接图)

如图，四个节点N1, N2, N3, N4全连接，共12条边。

定义他们的初始权重矩阵为0，如下表。

(TODO:补一个全0矩阵)

####目标：识别0101这个pattern。

####思路：

* 第一步，计算一个可以识别该pattern的矩阵，也叫做贡献矩阵(contribution Matrix)。

* 第二步，将该贡献矩阵与连接矩阵相加。(因为贡献矩阵可以识别目标pattern，于是该连接矩阵也算是学到了新的pattern。)


####具体操作：


* 第一步，计算极性对等式

    极性(Bipolar)，是因为binary的表示有点问题，0和1并不是相反数。于是我们通过一个简单公式来做极性转换。公式如下：

$$
f(x) = 2x-1
$$

对应的也有个极性转换回来的公式：

$$
f(x)=\fraq{x+1}{2}
$$

* 第二步，计算贡献矩阵

将[-1,1,-1,1]与其转置进行相乘，然后将对角线置为0，因为Hopfield的节点没有自己连接到自己。

结果如下：

（TODO：）补一个矩阵的表达式

* 第三步，将该贡献矩阵与连接矩阵相加。

由于初始矩阵使0矩阵，于是最终结果还是上面的贡献矩阵。

####推广

如果我们要计算可以识别0101和0110的Hopfield网络，只需要将这两个pattern的贡献矩阵相加就可以了。

####问题

这个pattern识别有一个副作用，当其识别了0101之后，1010与它是相反的，也会被自动识别。









（未完待续）








