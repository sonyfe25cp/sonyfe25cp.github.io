---
layout: post
title: "多标签分类算法 by Ivor W Tsang"
category: paper
---

###DATE

2015-05-13 计算中心 110房间

###TOPIC

Large Margin Metric Learning for Multi-Label Prediction

###Problem

图片带有多种标签，如何对新的图片进行标签预测，定义为multiple label classification问题。

###Review

2010: BR trains a binary classifier for each label

BR是传统方法，针对于multiple label，则训练对应的多个classifier，然后对未知图片进行挨个的0-1分类.

缺点很明显：

* 如果标签量很大，分类器的数目就很多。
* 并没有利用标签和标签之间潜在的关联关系，每个分类器中对于其他标签来说都是独立的。

| PX - Y' | 为距离，求最小值


MMOC方法：在BR的基础上，引入了新的向量空间，并考虑了标签之间的关系。

| VPX - VY' | 就相当于在原有基础上进行了空间转换。

缺点：

* 计算量很大，需要计算每个点到其他所有点的距离

存在的改进空间就是，只计算邻居，于是想到了KNN。




###Motivation

Deng 2010, KNN achevie supervior performance

似乎是这篇文章讲了KNN随着特征空间增长时的效率问题，等找到论文再看一下。

KNN sensitive to features

When features is large, knn wont work

###Approach

在MMOC的基础上，将全部点改为邻居点 ==》 ML-MMOC


###贡献

最大的贡献在于提高了效率，由于MMOC需要计算的点为全部点，也就是n\*logn，但knn之后只需要计算n\*k个点。

在效率上提升了很大，而结果跟原始MMOC差不多。

MMOC，MLMMOC在label数量少的情况下，相比于BR的方法差不多。

在label达到4000的时候，MMOC跟MLMMOC的F1为0.38，而传统BR只有0.037。

也就是说这类方法适应在大数据的情况下，当目标有大量的标签需要去标注，这个方法的优势就出现了。

同样，在大数据下，MMOC的计算量将非常大，而MLMMOC只需要计算邻居，于是速度很快，由KD-tree来存储就更快了。

###联想

本文是为了图片的标签预测而产生的方法，同样可以用在NLP上。

一个文档可以有多个标签（关键词），而这些个标签的产生就是类似的过程。

传统无监督方法有TFIDF，topic model

有监督方法 则就是BR这种了。




###Reference

MMOC: zhang and schneider 2012
