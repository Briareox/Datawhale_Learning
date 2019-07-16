# 【Task6】Hive原理及其使用
参考资料：[https://www.shiyanlou.com/courses/running](https://www.shiyanlou.com/courses/running)  
1. 认识Hive 

Hive通常用于数据仓库，可存储/查询结构化数据等，其优势在于存储量极大，可以存储数百亿/千亿（TB/PB）级别的数据。相较于传统的RDBMS，只能存储千万/亿级数据，有很大优势。
其底层存储依赖于HDFS，因此可以水平扩展，
元信息存储在derby或mysql中，
查询使用HQL，底层使用MapReduce实现。
2. Hive与传统RDBMS的区别

![图片](https://uploader.shimo.im/f/oDAkyhkq53QNPfmS.png!thumbnail)
3. HIve原理及架构图

![图片](https://uploader.shimo.im/f/HabOpjzJvqogI6mr.png!thumbnail)
a、 用户接口主要有三个： CLI， Client 和 WUI。其中最常用的是 CLI， Cli 启动的时候，
会同时启动一个 Hive 副本。 Client 是 Hive 的客户端，用户连接至 Hive Server。
在启动 Client 模式的时候，需要指出 Hive Server 所在节点，并且在该节点启动 Hive
Server。 WUI 是通过浏览器访问 Hive。
b、 元数据：Hive 将元数据存储在数据库中，如 mysql、 derby。 Hive 中的元数据包括表的名字，表的列和分区及其属性，表的属性（是否为外部表等），表的数据所在目录等。
c、 HQL：解释器、编译器、优化器完成 HQL 查询语句从词法分析、语法分析、编译、优化以及查询计划的生成。生成的查询计划存储在 HDFS 中，并在随后有 MapReduce 调用执行。
d、 Hadoop：Hive 的数据存储在 HDFS 中，大部分的查询由 MapReduce 完成（包含 * 的查询，比如 select * from tbl 不会生成 MapRedcue 任务）。
4. HQL（Hive中的SQL）

a. 准备数据
b.建表
```
CREATE EXTERNAL TABLE datawhale.user_action
(id INT,
uid STRING,
item_id STRING,
behavior_type INT,
item_category STRING,
visit_date DATE,
province STRING) COMMENT 'Welcome to datawhale!' 
ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t' STORED AS TEXTFILE LOCATION '/datawhale/user_action';
```
也可以先建表，再载入数据。

c. 查看存在的表
```
show tables;
```
查看表结构
```
desc datawhale.user_action;
```
d. 筛选10条看看
```
select * from datawhale.user_action limit 10;
```
e. 查询表内共多少行数据
```
select count(*) from datawhale.user_action;
```
f. 查询不重复的uid共多少行
```
select count(distinct uid) from datawhale.user_action;
```
g. 查询2014年12月10日到2014年12月14日有多少人浏览了商品
```
select count(*) from datawhale.user_action where behavior_type='1' and visit_date<='2014-12-14' and visit_date>='2014-12-10';
```
h 查询一件商品的购买率
i. 查询某一天在该网站购买商品超过5次的用户id
```
select uid from datawhale.user_action where behavior_type='4' and visit_date='2014-12-12' group by uid having count(behavior_type='4')>5;
```
h. 创建中间表，存储每个地区有多少用户浏览了网站
```
create table datawhale.province_user_dist(
province STRING,
view INT) 
COMMENT 'This is the count of user of each city' 
ROW FORMAT DELIMITED FIELDS 
TERMINATED BY '\t' STORED AS TEXTFILE;
```
#覆盖式的导入数据(有问题)
```
insert overwrite table datawhale.provice_user_count
 select province,count(behavior_type)
from datawhale.user_action 
where behavior_type='1' group by province;
```
Tips：试想，如果你的表特别的大，有没有什么可以优化的地方？
当然有，比如分区分桶，分区分桶可以从一定程度上提高查询效率。

5. Hive内部表/外部表/分区

内部表（managed table）
* 默认创建的是内部表（managed table），存储位置在hive.metastore.warehouse.dir设置，默认位置是/user/hive/warehouse。
* 导入数据的时候是将文件剪切（移动）到指定位置，即原有路径下文件不再存在
* 删除表的时候，数据和元数据都将被删除
* 默认创建的就是内部表create table xxx (xx xxx)

外部表（external table）
* 外部表文件可以在外部系统上，只要有访问权限就可以
* 外部表导入文件时不移动文件，仅仅是添加一个metadata
* 删除外部表时原数据不会被删除
* 分辨外部表内部表可以使用DESCRIBE FORMATTED table_name命令查看
* 创建外部表命令添加一个external即可，即create external table xxx (xxx)
* 外部表指向的数据发生变化的时候会自动更新，不用特殊处理

表分区（Partitioned table）
* 有些时候数据是有组织的，比方按日期/类型等分类，而查询数据的时候也经常只关心部分数据，比方说我只想查2017年8月8号，此时可以创建分区

数据准备：
movielen
user item rate thedate
分区表的创建：
```
create table datawhale.user_action_partition(
user      int comment "user id",
item      int comment "teim_id",
rate      int comment "rate"    
)
partitioned by (thedate string);
```
查询分区；
```
show partitions datawhale.user_action_partition;
```
插入分区。

删除分区。
```
ALTER TABLE  table_name DROP PARTITION (thedate='20000101')
```
分区表的查询。
思考一下，为什么要使用分区表？

参考:
[https://www.cnblogs.com/wswang/p/7718103.html](https://www.cnblogs.com/wswang/p/7718103.html)

6. HiveUDF

demo1：
这里用python自定义函数，去实现一个方法，利用身份证号去判断性别(18位身份证的倒数第二位偶数为女，奇数为男.15位身份证的倒数第一位偶数为女,奇数为男.).其实这个需求可以使用hive自带的function去进行解决.我们接下来使用2种方式去实现这个需求.
数据：
```
neil    411326199402110030
pony    41132519950911004x
jcak    12312423454556561
tony    412345671234908
```
HQL：
```
select idcard,
case when length(idcard) = 18 then
             case when substring(idcard,-2,1) % 2 = 1 then '男' 
             when substring(idcard,-2,1) % 2 = 0 then '女' 
             else 'unknown' end 
     when length(idcard) = 15 then 
            case when substring(idcard,-1,1) % 2 = 1 then '男'
            when substring(idcard,-1,1) % 2 = 0 then '女'
            else 'unknown' end
     else '不合法' end 
from person;
```
hive udf：
```
# -*- coding: utf-8 -*-
import sys

for line in sys.stdin:
    detail = line.strip().split("\t")
    if len(detail) != 2:
        continue
    else:
        name = detail[0]
        idcard = detail[1]
        if len(idcard) == 15:
            if int(idcard[-1]) % 2 == 0:
                print("\t".join([name,idcard,"女"]))
            else:
                print("\t".join([name,idcard,"男"]))
        elif len(idcard) == 18:
            if int(idcard[-2]) % 2 == 0:
                print("\t".join([name,idcard,"女"]))
            else:
                print("\t".join([name,idcard,"男"]))
        else:
            print("\t".join([name,idcard,"身份信息不合法!"]))
```

===测试
```
cat person.txt|python person.py
```
===udf
```
add file hiveudf.py
select transform(name,idcard) USING 'python person.py'  AS (name,idcard,gender) from person;
```

1. 文件开头的 add file 语句将 hiveudf.py 文件添加到分布式缓存，使群集中的所有节点都可访问该文件。
2. SELECT TRANSFORM ... USING 语句从 hivesampletable 中选择数据。 它还将 name、idcard值传递到 hiveudf.py 脚本。
3. AS 子句描述从 hiveudf.py 返回的字段。

脚本文件：
1. 从 STDIN 读取一行数据。
2. 尾随的换行符使用 string.strip()删除。
3. 执行流式处理时，一个行就包含了所有值，每两个值之间有一个制表符。 因此， string.split("\t") 可用于在每个制表符处拆分输入，并只返回字段。
4. 在处理完成后，必须将输出以单行形式写入到 STDOUT，并在每两个字段之间提供一个制表符。



参考：
1. [https://blog.csdn.net/qq_26937525/article/details/54136317](https://blog.csdn.net/qq_26937525/article/details/54136317)
2. [https://docs.azure.cn/zh-cn/hdinsight/hadoop/python-udf-hdinsight](https://docs.azure.cn/zh-cn/hdinsight/hadoop/python-udf-hdinsight)
