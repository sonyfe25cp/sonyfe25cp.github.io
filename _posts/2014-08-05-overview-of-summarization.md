---
layout: post
title: "Overview of Summarization"
category: paper
---

###为什么要做多文本摘要

###多文本摘要的方法

###ROUGE工具介绍

ROUGE（Recall-Oriented
Understudy for Gisting Evaluation）是在文本摘要领域中使用最多的评价工具。

这个基于召回率的评价标准是用来衡量一个摘要系统的好坏，根据机器产生的摘要覆盖了多少一个或者多个人工摘要的内容来判断。

基于召回率是为了鼓励系统能够尽可能多的包括文章中所有的重要的信息。

虽然召回率可以通过unigram，bigram，trigram甚至是4-gram来进行计算，但是Rouge-1（unigram）已经被认为是用来判断人工摘要和机器摘要相关性的最好的方法。

Rouge-1通过unigram出现在人工摘要和机器摘要中的数目来计算。

###References
rouge: http://haydn.isi.edu/ROUGE/

wiki:http://en.wikipedia.org/wiki/Automatic_summarization?oldid=494007923


在传统的相似度模型中，一个句子向量包含的词语太少，以至于不能够提供足够的信息来做相似度计算，因为相对于段落和篇章来说，句子所包含的信息实在太少了。先前的研究也表明，当句子中的关键词不足时，很难去判断这个句子的相似性，这也是新闻事件检测中一个关键的defeating factor[^2].

[^2]:H.L. Chieu and Y.K. Lee, “Query Based Event Extraction Along a Timeline,” Proc. 27th Ann. Int’l ACM SIGIR Conf. Research and Development in Information Retrieval (SIGIR ’04), pp. 425-432, 2004

向量空间模型的缺点反映了我们需要找一个更好的文档表示方法来与现有的余弦相似度进行共同使用。























