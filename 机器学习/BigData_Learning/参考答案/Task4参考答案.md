# 【Task4】MapReduce+MapReduce执行过程
1. MR原理

hadoop streaming 原理
![图片](https://uploader.shimo.im/f/C3e59L3B4aMHQ5Gf.png!thumbnail)
2. 使用Hadoop Streaming -python写出WordCount

Python版本
mapper
```
import sys
import re

p = re.compile(r'\w+')
for line in sys.stdin:
	ss = line.strip().split(' ')
	for s in ss:
		if len(p.findall(s))<1:
			continue
		s_low = p.findall(s)[0].lower()
		print(s_low+'\t'+'1')

```

reducer.py

```
import sys

cur_word = None
sum = 0
for line in sys.stdin:
	word,val = line.strip().split('\t')
	
	if cur_word==None:
		cur_word = word
	if cur_word!=word:
		print('%s\t%s'%(cur_word,sum)) 
		cur_word = word
		sum = 0
	sum+=int(val)
print('%s\t%s'%(cur_word,sum))	
```

run.sh
```
HADOOP_CMD="/path/to/hadoop"
STREAM_JAR_PATH="/path/to/hadoop-streaming-2.6.1.jar"

INPUT_FILE_PATH_1="/inputt"
OUTPUT_PATH="/output/wc"

$HADOOP_CMD jar $STREAM_JAR_PATH \
    -input $INPUT_FILE_PATH_1 \
    -output $OUTPUT_PATH \
    -mapper "python mapper.py" \
    -reducer "python reducer.py" \
    -file ./mapper.py \
    -file ./reducer.py
```

2. 使用mr计算movielen中每个用户的平均评分。

3. 使用mr实现去重任务。

4. 使用mr实现排序。

5. 使用mapreduce实现倒排索引。

6. 使用mr实现join功能。

7. 使用mapreduce计算Jaccard相似度。

8. 使用mapreduce实现PageRank。

[基于MapReduce的PageRank算法实现](https://blog.csdn.net/a819825294/article/details/53674353)

[Python3调用Hadoop的API](https://www.cnblogs.com/sss4/p/10443497.html)