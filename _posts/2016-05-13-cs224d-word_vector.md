##CS224D-Word Vector

###词语离散表示

可以作为补充资源，例如同义词。

缺点：

1. 缺少新词的关系
2. 客观
3. 需要人工标注
4. 难于计算相似度

问题：

在01向量表示中，一个词的向量中有太多0。

[0,0,0,0,0,0,1,0,0,0,0,0,0]，长度可能是整个字典的长度。

计算相似度时：

hotel=[0,0,0,0,0,0,1,0,0,0,0,0,0]

motel=[0,0,0,0,0,0,0,0,0,1,0,0,0]

Sim = 0

这不科学

###基于表示的分布相似性

利用上下文来计算词语的相似性

如何用邻居来表示词语？基于共现率矩阵

* 两种方式：整篇文档vs滑动窗口
* word-document 共现矩阵即可

####滑动窗口的共现矩阵

* 窗口长度 5-10
* 对称

例子：

* I like deep learning
* I like NLP
* I engjoy flying

0513/dl-vector-sample1.png

这样的矩阵有什么问题？

* 增加词典大小
* 维度太高
* 依然很稀疏

解决方法：低维向量表示

思路：存储最重要的信息在固定、较小维度的向量中：dense vector

通常25-1000维度

如何降维？

* SVD

从现在开始，全部的词都是一个dense向量X了。

###Hacks to X

问题：个别词(he, has, was)出现频率太高，导致syntax有太多影响。

* min(X,t)， with t~100
* Ignore them all

其他方法：
* Ramped windows that count closer words more
* 采用皮尔逊系数代替出现次数，然后将负数值定为0

如何可视化这些词语关系？

* 直接选前2维度
* PCA找到两维度
* SVD两维度

SVD存在的问题？

* 计算性能问题，O(mn^2)
* 对于过百万的词或者文档时，效果很差
* 引入新词的时候，比较麻烦

思路：直接学习如何低纬度表示

主流方法：word2vec

1. 直接计数出现次数
2. 预测每个词的上下文
3. 跟GloveVector差不多
4. 引入新句子或者新文档的时候很快

####Details of Word2vec

0513/dl-vector-details.png






































































