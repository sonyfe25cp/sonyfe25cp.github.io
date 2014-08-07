---
layout: post
title: "Overview of TDT"
category: paper
---

###TDT Overview

虽然及时获得信息变得越来越重要在今天的知识经济中,但是伴随着家庭和办公环境的网络的发展，它不再是一个问题。相对的，数字信息内容的高速传播和爆发式增长，却带来了新的问题，也就是信息过载。

很明显，人们在一定时间内可以接受的信息总量是有限的。

话题检测已经发展成一个非常重要的研究领域，它利用现代的计算技术来解决这个新生问题。

话题检测是话题检测与跟踪的一个子问题，它主要研究如何去识别和组织那些属于同一个话题的文本内容，以便我们可以将不同的信息来源融合成一个整体。然而在新闻这个场景下，话题识别可以看做是事件检测，也就是说从大量的新闻中识别出不同的事件。

###Sentence Modeling

在TDT领域中，通常采用传统的向量空间模型去比较两个文档的相似性。特别是在新事件发现和话题跟踪方面，余弦相似度方法经常被用来判断一个新文档是否与已有的某个事件相似。
尽管还有一些采用机器学习的方法在做语言跟踪和建模，但是向量空间模型在这方面还是最好的方法[^1].

[^1]:G. Kumaran and J. Allan, “Text Classification and Named Entities for New Event Detection,” Proc. 27th Ann. Int’l ACM SIGIR Conf. Research and Development in Information Retrieval (SIGIR ’04), pp. 297-304, 2004.

但是，当我们需要一个高准确率或者召回率的时候，它的局限性就非常明显。

在传统的相似度模型中，一个句子向量包含的词语太少，以至于不能够提供足够的信息来做相似度计算，因为相对于段落和篇章来说，句子所包含的信息实在太少了。先前的研究也表明，当句子中的关键词不足时，很难去判断这个句子的相似性，这也是新闻事件检测中一个关键的defeating factor[^2].

[^2]:H.L. Chieu and Y.K. Lee, “Query Based Event Extraction Along a Timeline,” Proc. 27th Ann. Int’l ACM SIGIR Conf. Research and Development in Information Retrieval (SIGIR ’04), pp. 425-432, 2004

向量空间模型的缺点反映了我们需要找一个更好的文档表示方法来与现有的余弦相似度进行共同使用。























