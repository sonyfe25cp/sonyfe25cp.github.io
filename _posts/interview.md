##Interview 

###Google

**欧美人**

	给两个list of string, A, B，判断A和B中的string是否是彼此的permutation。e.g., A=["a", "b"], B=["b", "a"], A是B中words的permutation
	
方法1：直接sort A和B，然后挨个比较，时间复杂度排序O(nlogn)+遍历O(n)

方法2：A和B分别放到一个hashmap中，然后挨个比较值。放map复杂度O(n)，挨个比较O(n)

	 现在同样给两个string， A和B，但是B特别大，可以想象成一个doc，判断B中是否有sub-list 是A的permutation，要求是consecutive sub list,
	 e.g. 
	 A= ["a", "b"], B=["c", "b", "a"], return yes；
	 A= ["a", "b"], B=["c", "b", "d", "a"], return no. 
	 
方法：先对A做一个hashmap，记录各字母的出现频率，以及A中总数，用于做window size。对B开始从头遍历，固定window大小的窗口，对比这个窗口里的内容与A是否一致，一致就ok；不一致，指针+1。

	Leetcode：Binary Tree Maximum Path Sum

方法：还没做


	




###Bloomberg