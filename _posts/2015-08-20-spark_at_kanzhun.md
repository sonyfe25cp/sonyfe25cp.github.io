

###问题

各位,请教一下, 我现在有一个单词文本,怎么创建一个key-value rdd, key为单词,value为从0开始的递增值，其中累加器task端使不能访问的，只能在driver端获取累加器的值

###方法1

1. mapPartion 拿到所有partion
2. 每个partion 有个base
	
	预先统计出每个partion的数量，然后放在driver端，然后遍历partion 做foreach

3. 遍历每个partion里面的东西 对应的 value 为 base+1

比如 有三个partion a,b,c
a = 1000个元素
b = 200 个
c = 300 个
然后a 的base 就是0
b 的base 是1000
c 的base 是1200

也就是说，遍历时候只能在driver，单个task顺序遍历  

	val indexToSize = rows.mapPartitions(iter =>Array(iter.size).iterator,true).collect().zipWithIndex.toMap

    rows.mapPartitionsWithIndex((index,iter)=> iter.zipWithIndex.map{f=> indexToSize(f._2) + index})


###方法2

1. 先把所有单词形成一个rdd,然后创建一个和这个单词对应的位置rdd,两个rdd做zip

代码如下：

	val fullTextRdd = textRdd.flatMap(_.split(" ")).map((_, 1)).reduceByKey(_+_)

	val wordsRdd = fullTextRdd.keys.repartition(1)
	val wordsCount = wordsRdd.count
	if (wordsCount <= 0){
	  println ("error, word count <= 0")
	  return
	}

	val listLocation = 0 to (wordsCount.toInt - 1)
	val locationRdd = sc.parallelize(listLocation, 1)

	val wordLocationRdd = wordsRdd.zip(locationRdd)