---
layout: post
title: "NLP && ML classes"
category: research
---


Paper ID	803

Title: 	A Resume Knowledge Extraction System and Key Technologies

###Masked Reviewer ID:	Assigned_Reviewer_1

Review:	

	Question : Is the submission relevant to the KM track?	Yes
	What do you think of the ideas in the submission?	Almost none
	Is the writing clear?	No
	Overall recommendation	-6: Must reject
	
How to improve the submission (for the camera-ready, if accepted or for resubmission to another venue, if rejected)? List your three suggestions.

* Approach doesn't seem to work (significantly) better than the opponent, a quite strong shortcoming that limits impact. 方法没有比别人好很多
* Manuscript needs to be carefully edited/rewritten. 书写还有问题
* Reconsider crisp problem statement and a devise a more principled approach. 考虑一下问题的描述，并设计一个更好的办法。
	
Detailed review	
	
	The paper addresses the problem of mining structured information out of textual resume descriptions.
	The introduction sketches a few application scenarios. Some of these do not exactly match what is considered in the paper. Specifically, portals in which users enter resume data seem not related.介绍部分对问题应用场景描述不好。对于直接在网站上进行简历填写这种情况没有提及。

	The paper describes adhoc, heuristics to retrieve specific information out of resumes.

	The evaluation compares the proposed methods to a competitor. It is not entirely clear to me how exactly precision and recall are defined in this setup. 准确率和召回率的定义并不清晰。
	In any case, the performance gains of the proposed approach are only marginal, if existent at all (in some cases).

	The paper appears to be quite preliminary work and not yet ready for publication.

	Next to the technical shortcomings, unfortunately, the quality of the manuscript is rather poor.书写太差。

###Masked Reviewer ID:	Assigned_Reviewer_2
Review:	

Question	 

	Is the submission relevant to the KM track?	Yes
	What do you think of the ideas in the submission?	Standard
	Is the writing clear?	No
	Overall recommendation	-4: Should reject

How to improve the submission (for the camera-ready, if accepted or for resubmission to another venue, if rejected)? List your three suggestions.

* Definitely need to check the text's typos. Unfortunately there are many of them. 拼写错误太多！

* Elaborate more in sections 3.5 and 3.6 (after all, there is plenty of space up to the 10-pages limit)还有太多空白地方可以写字！

* Be more clear on the experiments procedure. Do you have some kind of verified version for PROSPECT and CHM or not? Do the test data differ? 详细介绍实验处理过程。是否有另外两个系统的源码？

Detailed review	

	This paper is concerned with the problem of extracting structured data from CV's in a quick, automated way. They point out that head hunters, which have to deal with thousands of CV's, would surely benefit from an extraction system like the one they propose. 问题描述的还可以。
	At first, they notice that knowledge extraction from CV's can be relatively easy in case we know which template is being used. 
	In such cases, we can achieve complete accuracy by implementing the rule-based parser that corresponds to each template. 
	On the other hand, when we do not know and cannot infer the template, the problem is definitely more difficult. 
	The authors focus on the second case where we are in need of "free-text methods", rather than the first which they choose not to elaborate as it being a bit trivial. --重点研究非模板化的简历知识抽取。
	They present the pipeline of their framework. It consists of four steps. 
	First, the raw text is extracted and some noise (mainly unnecessary gaps) is eliminated. 
	In the second step, each sentence is classified into one of three basic sentence structures defined by the authors: Simple, Key-Value or Complex. As it has already been mentioned, the goal is to extract structured data from the CV. Since between these three types only the key-value type has some kind of structure, the authors consider the sentences of that kind to contain the candidate attributes. Apart from the sentence classification, the system also extracts a basic entity for each line. 
	In the third and fourth step, they try to locate the blocks whose template is repeated (if any) and match the attributes presented in them. For example, in the Work Experience section, there might exist more than one jobs which contain from-to dates, company, position. The system uses Naive Bayes for the text classification.
	In section 4, the authors evaluate their system. At first, they compare its Precision, Recall and F-1 metrics with those of two other opponents: PROSPECT[12] and CHM[15]. After that, they evaluate the system's performance on extracting the right information block-by-block (ex. precision, recall, F-1 for the attributes in the block about education).

	First of all, there is a clear problem in the use of English language. There are too many typos to point them out one by one. The paper at the current state is not at all presentable. ---拼写错误太多！！！
	The language errors, though, are probably not this work's major drawback .

	In the Block Classification(3.5) and the Attributes Match(3.6) parts, I think the authors should get into more details. 详细介绍下 分块 和 属性匹配

	In Block Classification(3.5), the probability of a word being a caption is defined to be proportional to its term frequency, although this does not really make sense: the captions are some kind of inner titles that define the beginning of a section; they don't have to be repeated in the resume text file. 

	In the evaluation section, the whole comparison with PROSPECT and CHM is of controversial validity. 
	In page 5, section 4.2, right column, the authors admit that "only two blocks' data were provided by these two models" and that the PROSPECT has higher precision and recall than the proposed method due to "the difference of test corpus"!! By that, the authors state that the testing data given to the three systems differ.
	
	From what I have understood and partially guessed, the authors might not have an implementation of these two opponent systems in their hands and simply re-publish precision, recall and F-1 percentages from [12] and [15].没有另外两个系统的实现，只是用了他们的结果。
	Plus, even if we considered the comparison valid, the results are not much in favour of this work's results.

	Although the authors' approach in general is reasonable and seems to work ok, the extracting system based on Conditional Random Field[15] seems being more scientifically stable. 似乎条件随机场的效果要稳定一些
	Plus, the argument that this approach "doesn't need too much manually annotated training set" is not that strong because the training happens once and for all.他认为节省标注并不是很大的优势，毕竟标注是一劳永逸的。

	Finally, I must mention that the authors reveal themselves (through a reference to a paper of theirs) despite the fact that it is a double-blind procedure. 又犯了一次double-blind的错误。

###Masked Reviewer ID:	Assigned_Reviewer_3
Review:	

Question	 

	Is the submission relevant to the KM track?	Yes
	What do you think of the ideas in the submission?	Standard
	Is the writing clear?	Yes
	Overall recommendation	-4: Should reject

How to improve the submission (for the camera-ready, if accepted or for resubmission to another venue, if rejected)? List your three suggestions.

* the novelty of the proposed framework should be improved
* the contribution is not clear

Detailed review	This paper proposed a framework that converts unstructured resume data to structured data by rules and templates based methods.

	I'm sure there are lots of engineering work related to resume extraction and I appreciate the pipeline that the authors proposed. 
	However, I feel the contribution of the proposed framework on KM and data mining is limited. The methods used in the paper are standard. 方法比较普通
	In addition, the authors did not show much detail on the techniques, thus it's difficult to determine exactly what the novelty of this paper is. 需要细节部分再重点描述。