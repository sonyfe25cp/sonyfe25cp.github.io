


Semantic Analysis and Helpfulness Prediction of Text for Online Product Reviews

作者认为评论的有用性取决于文本的内部，而不是外部因素。

Ground Truth：用户的scoring of helpfulness

人工标注400条


###Introduction写作逻辑：

产品评论开始影响用户的购买决策。随着产品评论的增多，开始用推荐或者排序的方法来展示。

现有的方法大多引入了很多外部因素，如时间、产品类型等，使得模型不具有可移植性。

于是需要将这些外部因素与内在因素区分开，从内部来解决这个问题，使得模型可以更好的移植。这同样使得该方法对评论摘要也有帮助。

近期的NLP研究探讨了文本风格与其属性（如可读性、信息性、可信程度）之间的关系。我们假定有用性也是文本内部的一种属性。

为了理解文本中的内容，实现了现有的句法和词典，并将评论表示为语义向量。而且还引入了两个新的特征，LIWC和INQUIRER。引入这俩的原因在于，人们通常将情感和推理引入到文本中，这使得文本更具有价值。

先前的工作一般采用了X of Y helpfulness的方式来当做标注数据，由于这里面存在大量的偏差，于是我们还是让人工标注了一批。

我们将这个预测文本有用性的问题作为一个回归问题。实验证明，只用文本上的特征也可以取得很好的结果。而新引入的两个特征显著的提升了结果。然后我们还分析了语义特征并且发现Psychological Process对文本有用性起到了很重要的作用。反映思考的词有助于有用性，而情感则并没有。最后，在人工标注的数据上实验结果更好。

###数据

Amazon Review Dataset共包含35million的评论，覆盖1995到2013年。其中一个子集为696696条，覆盖4个领域：Books，Home，Outdoors，Electronics。

对每一个分类，选择评论最多的100个产品来分析，每一个评论都要至少有5个vote，剩下115880条，构成了自动标注数据集。

然后每个分类中随机选择100个评论，构成了人工标注数据集，已公开。

###特征

STR：total number of tokens, total number of sentences, average of tokens, total number of sentences, average length of sentences, number of exclamation marks and  percentage of question sentences.

UGR：去掉停词和低频词，TF-IDF特征。

GALC：36种有效状态。构建了一个特征向量，包含每个表情的出现次数和额外的情感词。

LIWC：LIWC是一个词典，用来决定句子的正负面情感程度。

INQUIRER：General Inquirer是一个词典，其中的词按照类别分组。不同的词被映射到不同的语义标签。如，absurd 被映射到 NEG和VICE。

###实验

目的：1，一个类比训练好的模型是否可以直接应用于第二个类别；2，什么元素使得文本的有用性比较高。

先来组合特征并对比，传统的STR和UGR特征只是作为baseline，把GALC，LIWC，INQUIRER作为一组，把全部作为另一组。

模型：SVM regression with RBF kernel

标注：vote产生的自动标注和人工标注

评价标准：RMSE 和 皮尔逊相关系数

####自动标注数据集上的实验

从A上训练的模型移植到B上的结果衡量标准不应该直接这么看，而是应该对比该模型在A上的效果。
于是，先计算model_A在B上的结果，然后除model_A在A上的结果，这样做一个正则化。

由于从图上来看STR跟INQUIRER的效果一直领先其他，而UGR几乎最差，这就说明了UGR基本是不可迁移，因为它在独立测试时效果很好；STR是可迁移的，而它再独立测试时效果一般；那么就说明了，INQUIRER跟LIWC是起了作用的。

####什么起了决定性因素

LIWC和INQUIRER不仅提供了更好的结果，而且还有助于理解什么使得评论内容有用。

在LIWC中的因素分析情况如下：

经分析Relativity、Time、Inclusive、Postive Emotion和Cognitive Processes这5类对LIWC和INQUIRER是最相关，而它们都属于心理过程，也就是说人们在比较有思路的时候写的东西是有帮助的。

在INQUIRER中的情况如下：

* Vary、Begin、Exert、Vice和Undrst这些词属于process or change 类，例如start、happen和break。
* Vice标签包含的词暗示道德上的反对等。
* Understated标签的词暗示在某些领域降低重要性或者谨慎，通常反映缺乏情感表达



























