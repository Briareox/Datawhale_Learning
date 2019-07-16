
# 【Task6】Spark常用API
1. 认识Spark

1.1 Spark的组件
Driver
Cluster Manager
Worker
1.2 Spark的运行模式
![图片](https://uploader.shimo.im/f/SSa5pJaGzo8dsJVX.png!thumbnail)
2. Spark的RDD

RDD是弹性分布式数据集。
弹性的意思是当保存RDD的一台机器遇到错误时，Spark可以根据lineage谱系图重新计算出这些RDD。
分布式的意思是，这些对象集合（分区）被分布式的存储在集群中的不同节点上。
Spark 中的 RDD 就是一个不可变的分布式对象集合。每个 RDD 都被分为多个分区，这些分区在集群中的不同节点上。
初次之外，RDD还包含了一些操作API，例如常见的map，reduce，filter等。


1. 使用shell方式操作Spark

Tips：建议先准备在home目录下新建workspace/learnspark/目录
```
cd ~
mkdir -r workspace/learnspark/
cd workspace/learnspark/
```
vim test.txt 输入i进入编辑模式
![图片](https://uploader.shimo.im/f/YBTdQuvjZmcOMw4p.png!thumbnail)
保存退出(esc+:wq)

a. 进入交互式编程环境，打开终端，在终端输入pyspark，便可进入pyspark环境。进入pyspark环境后，会初始化好SparkContext。
![图片](https://uploader.shimo.im/f/HtDpzdLFTEcTDySN.png!thumbnail)
b. 创建rdd
通常有两种方式创建RDD，一种是读取外部数据集，另外一种是使用已有的对象集合。
1. 使用已有的对象集合

![图片](https://uploader.shimo.im/f/hsY7c66eALwFMiDd.png!thumbnail)
2. 读取外部数据集(注意更换目录)

![图片](https://uploader.shimo.im/f/NXGUxVEHgUYQqYTE.png!thumbnail)
1. 基础的API操作，为了简便，下面如果未声明，均使用第一种方式创建rdd，这也是在工作中快速验证rdd API功能的最简便快捷的方式。

map：数据集中的每个元素经过用户自定义的函数转换形成一个新的RDD
![图片](https://uploader.shimo.im/f/FzR9wr6B95wyqHiP.png!thumbnail)
我们自定义了一个lambda函数，使得每个元素+1.
那么，这和普通的python list操作有什么区别的？试想，当list的元素在百万，千万级别的时候，python还尚能处理这种逻辑，假如集合中的元素是百亿，千亿级别呢，这个时候就需要依靠Spark处理了。这也正是RDD要解决的问题，海量大数据处理。
map示意图，其中红色方框代表分区
![图片](https://uploader.shimo.im/f/FQUIV6h4GekksRCc.png!thumbnail)
union 两个rdd的并集
![图片](https://uploader.shimo.im/f/RdMtyRfXuN8T4iPV.png!thumbnail)
intersection 两个rdd的交集，注意这里声明了一个新的rdd7= 5，2，1和rdd5=5，2，0的交集是5，2
![图片](https://uploader.shimo.im/f/gTGsg7oxYQcBDfTg.png!thumbnail)
filter: 过滤，筛选出符合条件的元素
![图片](https://uploader.shimo.im/f/0XusZIDPAWIy3S1R.png!thumbnail)
distinct：去重
![图片](https://uploader.shimo.im/f/XGz9rohJT5g5No2E.png!thumbnail)
cartesian：笛卡儿积
![图片](https://uploader.shimo.im/f/xcSKgUY8sjIG3kLx.png!thumbnail)
reduce： 将rdd中元素两两传递给输入函数，同时产生一个新的值，新产生的值与RDD中下一个元素再被传递给输入函数直到最后只有一个值为止。
![图片](https://uploader.shimo.im/f/JEhe2De5rM83vVke.png!thumbnail)
groupByKey：对于kv类型的数据，按key分组
![图片](https://uploader.shimo.im/f/F95ytsCVFRg3q5cx.png!thumbnail)

需要说明的是，spark RDD的算子分为两种，一种是transformation，例如map，filter；另外一种是action，例如collect(),count(),groupByKey()等。只有action算子才会真正的触发计算。

RDD API问题与作业
1. 尽可能多的练习RDD的其他API，包括sortBy,join,reduceByKey,take,first,count,countByKey等。
2. 整理自己学习过的RDD算子，并给他们按照transformation和action进行归类，画出思维导图。
3. 说一说take,collect,first的区别，为什么不建议使用collect？(虽然我用了。。。)
1. 向集群提交Spark程序

可以在jupyter book中使用spark，也可以在pySpark中直接使用（会自动创建spark context）
不过更常用的是，将job提交到集群中运行。
4.1 先来体验一下Spark版本的wordcount吧
wc.py
```
import os
from pyspark import SparkConf,SparkContext

conf = SparkConf().setMaster('local').setAppName('word count')
sc = SparkContext(conf = conf)
```

#注意将这里改成自己的路径 
```
import os
from pyspark import SparkConf,SparkContext

conf = SparkConf().setMaster('local').setAppName('word count')
sc = SparkContext(conf = conf)

#注意将这里改成自己的路径 
text_file = sc.textFile('hdfs:///test/The_Man_of_Property.txt')
 
count = text_file.flatMap(lambda line:line.split(" ")).map(lambda word:(word,1)).reduceByKey(lambda a,b:a+b).sortBy(ascending=False, numPartitions=None, keyfunc = lambda x: x[1]) 

print(count.take(10))

#拿出一些结果先看看
print(count_rdd.take(50))
#保存结果
count_rdd.saveAsTextFile('hdfs:///datawhale/out/')
```

**提交：****bin/spark-submit --master spark://host:port --executor-memory 2g wc.py**
4.2 wordcount 的另外一种写法
```
def splitx():
    return line.split(" ")

count = textfile.flatMap(splitx) \
                    .map(lambda word:(word,1)) \
                    .reduceByKey(lambda a,b:a+b)
```
这里想说明的是，map/reduce等算子，不仅可以接受lamda函数作为参数，也可以接受自定义的函数，直接把函数名传进去即可。


任务：
1. 使用上述API计算《The man of property》中共出现过多少不重复的单词，以及出现次数最多的10个单词。

movielen 数据集：[http://files.grouplens.org/datasets/movielens/ml-1m.zip](http://files.grouplens.org/datasets/movielens/ml-1m.zip)
2. 计算出movielen中，每个用户最喜欢的前5部电影。
```
运行pyspark
import pandas as pd
# 读取文件
user_data = sc.textFile("/test/users.dat")
movie_data = sc.textFile("/test/movies.dat")
ratings_data = sc.textFile("/test/ratings.dat")
# 切分数据
user_rdd = user_data.map(lambda line: line.split("::"))
movie_rdd = movie_data.map(lambda line: line.split("::"))
ratings_rdd = ratings_data.map(lambda line: line.split("::"))
# 将RDD转化为DF
user_df = sqlContext.createDataFrame(user_rdd).toPandas()
movie_df = sqlContext.createDataFrame(movie_rdd).toPandas()
ratings_df = sqlContext.createDataFrame(ratings_rdd).toPandas()
# Rename列名
user_df.columns = ['UserID','Gender',"Age","Occupation","Zip-code"]
ratings_df.columns = ['UserID','MovieID',"Rating","Timestamp"]
movie_df.columns = ['MovieID','Title',"Genres"]
#将三张表合并成一张表
total_df = pd.merge(ratings_df,user_df,on = ["UserID"],how = "right")
total_df = pd.merge(total_df,movie_df,on = ["MovieID"],how = "left")
# 聚合操作
c = total_df["Title"].groupby(total_df["UserID"])
# 取出前5ge
second = c.agg(lambda x: x.value_counts().index[1]).reset_index()
first = c.agg(lambda x: x.value_counts().index[0]).reset_index()
third = c.agg(lambda x: x.value_counts().index[2]).reset_index()
fourth = c.agg(lambda x: x.value_counts().index[3]).reset_index()
fifth = c.agg(lambda x: x.value_counts().index[4]).reset_index()
# 创建新的的DF
like = pd.DataFrame()
like["UserID"] = first.UserID
like["first"] = first.Title
like["second"] = second.Title
like["third"] = third.Title
like["fourth"] = fourth.Title
```
3. like["fifth"] = fifth.Title

print(like[:5])

计算出movielen数据集中，平均评分最高的五个电影。
```
total_df.groupby('Title')['Rating'].mean().reset_index().sort_values("Rating",ascending = False)[:5]
```
4. 【选做】 计算出movielen用户的行为相似度（相似度采用Jaccard相似度）。

参考资料：[远程连接jupyter](https://blog.csdn.net/qq_18293213/article/details/72910834)

【没有jblas库解决办法】
下载jblas包 ：[https://pan.baidu.com/s/1o8w6Wem](https://pan.baidu.com/s/1o8w6Wem)
运行spark-shell时添加jar：spark-shell --jars [jblas path] /jblas-1.2.4.jar

