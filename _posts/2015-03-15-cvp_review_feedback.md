---
layout: post
title: "WAIM论文的reviews"
category: research
---

###Masked Reviewer ID:	Assigned_Reviewer_1
Review:	

Question	 ：

Overall Rating：**Strong Reject**

Strong Points	-

Weak Points	-

**Detailed Comments：**	Not adhering with WAIM's double-blind reviewing process, the authors' names are on the paper. Thus, the paper has to be rejected without review.

	唉，自己没认真看投稿要求，太2b了

###Masked Reviewer ID:	Assigned_Reviewer_2
Review:	
Question	 

Overall Rating ：	**Accept**

Strong Points	：

The paper is well-motivated and easy to follow.
The paper proposed a simple,straight, but reasonable framework for resume information extraction.
Experiments on a real world data set of Chinese resumes shows the effectiveness of the proposed method.

	容易follow、虽然简单和直接，但是框架合理。在实际数据集上做了实验并证明有效。
	
**Weak Points**：
	
The reference is too limited. The authors can add more paper on Web IE.

	ok，增加点WEB IE相关的参考文献
	
The experiment can be further improved.
	
	实验还不够全面。
	
**Detailed Comments**	

In this paper, the authors propose a framework to extract knowledge about the person for building a structured resume repository. 

The proposed framework includes two major processes: the first is to segment text into semi-structured data based on Writing Style, and the second is to further extract knowledge from the semi-structured data with text classifier.
Generally, the paper is well-motivated and easy to follow. 

It proposed a simple,straight, but reasonable framework for resume information extraction. 

Experiments on a real world data set of Chinese resumes shows the effectiveness of the proposed method. 

**Some suggestions:** 

1) The reference is too limited. The authors can add more paper on Web IE. 

	ok，再补引用和文献。

2) Table 2 shows, the performance of the proposed method is not as good as PROSPECT. The reason by the authors is that, the results are acquired on different corpus. The authors are expected to realize and test the PROSPECT algorithm on the same corpus.

	希望能把PROSPECT在自己数据集上跑一边，这不现实啊。CRF标注的话，差距太明显了。
​
###Masked Reviewer ID:	Assigned_Reviewer_3
Review:	

Question	 

Overall Rating ：	**Neutral**

**Strong Points	：**

(1) topic is interesting.

(2) solution is useful according to the experimental results. 

**Weak Points**：

(1) utility on resumes in other languages;

(2) presentations could be further improved. 

(3) lack of details about the proposed method.

**Detailed Comments**：

This paper proposes a framework to detect the important information in resumes. 

The experimental results demonstrate the effectiveness of the proposed method. 

Some minor comments/suggestions are provided as follows, which may help improve this paper: 

During the procedure of pre-processing, the authors removed the format, such as table. Is it possible to make better use of the table format? 

	如果用了table信息，效果会好么？应该解释为增加信息肯定能够让解析更加精确，但是局限性比较大；相反，不用这些信息的话，可以提供方法的适应性，尤其是面对大数据。

The authors defined three types of line type. It would be more interesting if more experimental results with analysis could be provided about each type.

	什么叫对每个类型进行分析？比例？


Table 2-4 use F1 as one of the evaluation measures. However, Table 5-7, the authors use “F-value”.

	额，不严谨，应该统一为F-1

More room could remained for the details of block classification and attribute match in Section 3.5 and 3.6 are required. For example, spaces for Table 2-4 could be condensed. 

	还能再节省点空白，以便更好的解释block classification和attribute match。


The authors should explained Algorithm 1 with more details.

	再解释下Alg 1

More explanations about evaluation and obtaining ground truth should be added. 

	增加下如何evaluation和ground truth。

This paper evaluates the proposed solution on Chinese resume. Further work could be investigating the difference of solution on Chinese and English. 

	后期工作可以去处理下英文的简历。

The proposed solution in this paper is reasonable and could be more generalized. 

	方案合理，应该可以更加泛化。

Additional space should be removed from the first sentence of Section 3.
Typos: 
are wrote -> are written
Should be contains -> Should be contained
Which often shown -> Which is often shown.
An novel-> A novel
This method need -> This method needs. 
Should be remove -> Should be removed

	居然还有语法错误。。