---
layout: post
title: "Reviews about multiple document summarization"
category: research
---


Paper ID	126

###Masked Reviewer ID:	Assigned_Reviewer_1

Review:	
Question	 
What is your confidence in rating this paper?
	
	Low - a significant part of the paper is outside of my expertise
	
	(⊙o⊙)…你居然说自己不懂这块

Overall Rating	
	
	Accept

How would you rate the novelty of the problem solved in this paper?

	A minor variation of some well studied problems

How would you rate the technical ideas and development in this paper?

	The technical development is incremental without fundamental contributions
	
	恩，确实不是基础贡献。

How would you rate the empirical study conducted in this paper?

	Thorough, including systematic comparison with proper state-of-the-art methods and novel case studies

How would you rate the quality of presentation?

	A very well-written paper, a pleasure to read. No obvious flaws.
	
	(⊙o⊙)…历史第一次被评论well-written

Should the Best Paper Committee consider this paper for an award?
	
	No
	
	果然非基础性创新不可能拿best award。
	
Detailed Comments	

	The paper provides a method for multi-document news summarisation based on a wide range of features covering the content and the time components of a news feed. 
	The authors compared against what appears to me to be the state of the art (though i am not an expert in the area) and do so on standard test collections with standard metrics, which should make it easy for future researchers to use this study. 
	
	呵呵，这位老师果然是外行么。。这对比的哪是state of art啊。。

	Of course, there are a number of issues which could, and in some cases should, be improved upon:
	- unclarities or lack of details:
	-- how are \beta and \alpha in Eq2 determined ? What is their influence on the results?
	
	ok，再介绍下如何确定Eq2中的alpha和beta。
	
	-- what is the effect of changing the test collection the way the authors do (removing sentences from days). Why is that necessary at all, compared to just grouping the sentences by day. Wouldn't this procedure change the summaries in such a way to make them incomprehensible to the human?
	
	解释为什么要这样处理数据集，这样做会不会改变了摘要的初衷。
	
	-- how exactly is the machine learning done? I'm assuming 5-fold cross validation, but perhaps the authors did something else. 
	
	ML部分到底怎么做的
	
	- presentation issues:
	-- the statement "LSA is introduced to [...] find the core words of a document" is not quite true. It can be used to do so, but I wouldn't say it has been introduced to do that. Presumably the authors use the term-vectors to compute similarities with the document vectors and rank terms for each document, but the paper fails to describe this procedure. 
	-- acronym TS is used without definition (presumably timeline summary) √
	-- modes --> models √
	-- "However" (just after Eq 3) introduces a contradiction to a previous statement. It's not clear what the contradiction should be here. √
		
		擦，一坨语法错误
		
	- other thoughts:
		-- why use TF when other weighting schemes are proven better (BM25, for instance)
		-- wouldn't it make more sense to use a new text at time t+1 to identify a sentence summarizing the events at time t? 
		


###Masked Reviewer ID:	Assigned_Reviewer_2
Review:	
Question	 

What is your confidence in rating this paper?

	Medium - knowledgeable on this topic
	
	看来是了解摘要的老师。

Overall Rating	

	Accept

How would you rate the novelty of the problem solved in this paper?

	A minor variation of some well studied problems

How would you rate the technical ideas and development in this paper?

	Substantial improvement over state-of-the-art methods
	
	呵呵

How would you rate the empirical study conducted in this paper?

	Not thorough, or even faulty Not applicable (the paper does
	
How would you rate the quality of presentation?

	Well-written but has a significant number of typos and/or grammatical errors. Would need significant editing before publication.
	
	写的还行，但是一堆语法错误。。擦擦擦

Should the Best Paper Committee consider this paper for an award?
	
	No

Detailed Comments	

	This paper proposes an approach to summarize multi-news related to an event in timeline considering the life circle feature of events.
	The strong point is that the authors compare their approach with other existing methods.
	However, it will be better to evaluate the precision and recall in the cases that utilize any four of five features, and thus clarify the effectiveness of each type of features.
	
	最好还是要有各种特征间的比较。。
	
	Another weak point is that there are many grammatical errors and both abbreviations and full titles of conferences intermingle in the references.
	
	语法错误还是多的不要不要的,参考文献中会议名字有全称有缩写。

###Masked Reviewer ID:	Assigned_Reviewer_3
Review:	
Question	 

What is your confidence in rating this paper?
	
	Medium - knowledgeable on this topic
	
	这老师懂。

Overall Rating	
	
	Neutral

How would you rate the novelty of the problem solved in this paper?

	A well established problem

How would you rate the technical ideas and development in this paper?
	
	The technical development is incremental without fundamental contributions

How would you rate the empirical study conducted in this paper?

	Thorough, including systematic comparison with proper state-of-the-art methods and novel case studies

How would you rate the quality of presentation?

	A very well-written paper, a pleasure to read. No obvious flaws.

Should the Best Paper Committee consider this paper for an award?

	No

Detailed Comments	

	This paper introduces a summarizing algorithm for multi-news timelines. This method employs the four types of features: surface features, importance features, aging features, and novelty features. In order to evaluate the effectiveness of the proposed algorithm, they conducted some experiments using two datasets: DUC-2002 dataset and TAC-2010 dataset. The results shows that their algorithm provides better performances than various conventional algorithms. 
	I couldn’t say that the approach used in this method has remarkable novelty. However their work has made some contributions.
	
	虽然这方法没啥牛逼的创新点，但还凑合吧。擦。。


 