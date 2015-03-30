---
layout: post
title: "Reviews about mds paper from Caise2015"
category: paper
---


###First reviewer's review:

####Summary of the submission

This paper describes a method for summarizing news articles in terms of timelines.
The paper presents a pipeline process and introduces several features for classification.
A SVM is used to classify sentences into summary or not. Experiments on DUC-2002 and TAC-2010 data sets are conducted.

####Evaluation 

The paper falls in the traditional category of text classification using machine learning. 
	
	Although several features are introduced for sentence classification, it is not clear how novel the method is.

额，新特征不算创新性么。。怎么才能有创新性呢？

	Experimental results showed that the proposed method outperformed some baselines. However, it is not clear what features made the method better than baselines.

需要再进一步分析是哪些特征对结果起了帮助作用。我觉得是这个reviewer没看懂啊，或者是他不了解mds，不然应该看出来我是在提新特征啊。

	The paper mainly presents a blackbox for some engineering tasks. It lacks a deeper study on why it works and how it advances the state of the art.

还是需要进一步说明到底是哪地方比原来的方法要好。


###Second reviewer's review:

####Summary of the submission

The paper presents an approach to extract timeline summary texts from multiple
news sources regarding the same event. The approach uses aging theory to
classify documents into the phases of the life-cycle of events.
Challenges attacked by the work are
* computing the importance of sentences in documents
* considering aging theory for this importance estimation
An SVM is trained for recognizing appropriate summary sentences.

####Evaluation

The idea of using aging theory for discovering the stage an event is in, and
using this information for summarization (or other event-related tasks) is
compelling.

引入aging这个因素，看来是被接受的。


	The page limit for CAiSE is 15 pages and this paper has only little more than 11 pages. On the other hand, it could benefit from some more discussion in several sections. The text is enumerative in some places. 
	
这是说，如果限制15页，那我就应该写到14页？

	For instance, in 2.1 (Related work on summarization) it would be good to get some more details about the advantages and disadvantages of the different approaches.

ok,给2.1节再修改一下，对比优缺点。

	Statements like "it's proved that..." should be underpinned by an appropriate cite.

ok，添加相应的参考文献。


	You say the timeline summary of each day should describe the most important thing that happened on that day. So, do you assume the user is only interested in at most one event per day?
	
表达的问题。由于数据场景是同一个事件，而这个事件在当天肯定是有重点的，于是摘要应该选择的是当天最重要的事情。因为，一个事件在某一天的摘要句就两三句。

	According to your definition of the "Input", I assume m denotes the number of days. Thus, there are m days. I wonder how come that there are m input sentences in S. Is there only one sentence per day?

表达问题。

	In your definition of "Aging feature" it is unclear what F denotes and I am wondering what we gain by multiplying F with F^-1. You also mention a decay factor beta which I cannot find in any of the formulas.
	
beta..F^-1,确认公式，确认参数。

	Given a word w you mention the variance of w but probably you mean the variance of the energy?
	
是的。同一个词在不同day有不同的能量。

	In your definition of the Topic feature you mention that you use PageRank to compute the latent semantic between sentences. Here, it is a bit unclear how the links are defined. I think this is an interesting idea and would have liked to see some more details about this approach, e.g., an example graph, and illustrative results.
 
表达不清楚。由于句子之间有相似度，于是就构成了一个相似度的图，这样就可以计算pageRank。

 
	You don't define what is i in sentence_i and frankly I think it is unnecessary as "sentence" is already a variable.
	
是的。

	Again, 4.3 is a bit enumerative and could use a few more details on the different methods.
	
ok，没有讲清楚不同方法的差异。

	For the evaluation you use two different setups (1 and 2) where you apply over-sampling to improve the performance in 2. This, however, is not done for the competing approaches. Therefore, IMHO the competition must be compared to your approach 1. In this setting though, your approach 1, while showing decent results, is constantly dominated by L2RTS. Therefore, I think your statement that it performs better than L2RTS is not entirely true. I wonder how L2RTS performs with oversampling.

方法1采用了全部特征+svm训练
方法2采用了全部特征+svm+oversampling
若证明特征起作用，应该方法1 》L2RTS，而方法2 》方法1 只能说明oversampling也有作用，并不能说明是特征起了作用。
于是需要对比一下，当L2RTS也用oversampling的话，结果是谁比较好。
那么L2RTS如果也用oversampling呢？补一组实验。



The language could be improved. Common problems are:

* missing / superfluous articles: (The) timeline summarization help...
* wrong grammatical person: Timeline summarization help(s)..., clusters and _applies_

* editorial problems: A particular challenge .. is (remove: how to) computing the importance
* typos: modes --> models, respesent --> represent, assigin --> assign



###Third reviewer's review:

####Summary of the submission

This paper discusses how to make summary from multi documents in news event timeline. The timeline summarization is needed to reorganize the sentence order
for a better reading experience. Experiments on public dataset show that this method is better than existing works.

####Evaluation

In the abstract, the authors stated that their method consists of 4 steps,
but these steps was not explicitly shown in the paper.

	What are Rouge-1 and Rouge-2 ?

额，你不知道Rouge-1和Rouge-2的话，你也不应该来审这篇稿子。
具体如何计算Rouge-1呢？？

	Give further explanation for Fig.1. What is the unique step/feature that make this method different from other similar works?

**如果要分析是特征起了作用，需要将特征进行排列组合，验证确实是增加了特征才使得结果变好！**
	


