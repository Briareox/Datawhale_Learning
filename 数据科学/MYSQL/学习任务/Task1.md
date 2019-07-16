负责人：某某某
# 【任务一】

## 1.1 MySQL 软件安装及数据库基础
**#学习内容#**
1.软件安装及服务器设置。
   教程 [http://www.runoob.com/mysql/mysql-install.html](http://www.runoob.com/mysql/mysql-install.html)

2.(选做，但是强烈建议) 使用图形界面软件 Navicat for SQL
   群里提供破解版Navicat for SQL，看群公告或聊天记录搜索888查找。
   简易步骤:
      解压缩文件，复制key
      打开文件夹中的navicat.e
      用户名随意，输入key，然后连接数据库
      输入密码，连接名改成自己喜欢的
      剩下的自己探索，怎么在navicat中创建数据库、表等等

3.数据库基础知识
   数据库定义
   关系型数据库
   二维表
   行
   列
   主键
   外键

4.MySQL数据库管理系统
   数据库
   数据表
   视图
   存储过程
## 1.2 MySQL 基础 （一）- 查询语句
#学习内容#
1. 导入示例数据库，教程 [https://www.yiibai.com/mysql/how-to-load-sample-database-into-mysql-database-server.html](https://www.yiibai.com/mysql/how-to-load-sample-database-into-mysql-database-server.html)

2. SQL是什么？MySQL是什么？

3. 查询语句 SELECT FROM 

    语句解释
    去重语句
    前N个语句
    CASE...END判断语句
4. 筛选语句 WHERE 

    语句解释
    运算符/通配符/操作符
5. 分组语句 GROUP BY

    聚集函数
    语句解释
    HAVING子句
6. 排序语句 ORDER BY 

    语句解释
    正序、逆序
7. 函数

    时间函数
    数值函数
    字符串函数
8. SQL注释

9. SQL代码规范

    [SQL编程格式的优化建议] [https://zhuanlan.zhihu.com/p/27466166](https://zhuanlan.zhihu.com/p/27466166)
    [SQL Style Guide] [https://www.sqlstyle.guide/](https://www.sqlstyle.guide/)

#作业#
项目一：查找重复的电子邮箱（难度：简单）
创建 email表，并插入如下三行数据
+----+---------+
| Id | Email   |
+----+---------+
| 1  | a@b.com |
| 2  | c@d.com |
| 3  | a@b.com |
+----+---------+

编写一个 SQL 查询，查找 Email 表中所有重复的电子邮箱。
根据以上输入，你的查询应返回以下结果：
+---------+
| Email   |
+---------+
| a@b.com |
+---------+
说明：所有电子邮箱都是小写字母。

项目二：查找大国（难度：简单）
创建如下 World 表
+-----------------+------------+------------+--------------+---------------+
| name            | continent  | area       | population   | gdp           |
+-----------------+------------+------------+--------------+---------------+
| Afghanistan     | Asia       | 652230     | 25500100     | 20343000      |
| Albania         | Europe     | 28748      | 2831741      | 12960000      |
| Algeria         | Africa     | 2381741    | 37100000     | 188681000     |
| Andorra         | Europe     | 468        | 78115        | 3712000       |
| Angola          | Africa     | 1246700    | 20609294     | 100990000     |
+-----------------+------------+------------+--------------+---------------+
如果一个国家的面积超过300万平方公里，或者(人口超过2500万并且gdp超过2000万)，那么这个国家就是大国家。
编写一个SQL查询，输出表中所有大国家的名称、人口和面积。
例如，根据上表，我们应该输出:
+--------------+-------------+--------------+
| name         | population  | area         |
+--------------+-------------+--------------+
| Afghanistan  | 25500100    | 652230       |
| Algeria      | 37100000    | 2381741      |
+--------------+-------------+--------------+

#彩蛋#

考虑到本次集训有很多新手，本次作业赠送建表代码，意不意外，开不开心。

直接将附件code内容复制到cmd或者navicat运行就行。

**项目一**
-- 创建表
```
CREATE TABLE email (
ID INT NOT NULL PRIMARY KEY,
Email VARCHAR(255)
);
```
-- 插入数据
```
INSERT INTO email VALUES('1','a@b.com');
INSERT INTO email VALUES('2','c@d.com');
INSERT INTO email VALUES('3','a@b.com');
```

**项目二**
-- 创建表
```
CREATE TABLE World (
name VARCHAR(50) NOT NULL,
continent VARCHAR(50) NOT NULL,
area INT NOT NULL,
population INT NOT NULL,
gdp INT NOT NULL
);
```
-- 插入数据
```
INSERT INTO World
  VALUES('Afghanistan','Asia',652230,25500100,20343000);
INSERT INTO World 
  VALUES('Albania','Europe',28748,2831741,12960000);
INSERT INTO World 
  VALUES('Algeria','Africa',2381741,37100000,188681000);
INSERT INTO World
  VALUES('Andorra','Europe',468,78115,3712000);
INSERT INTO World
  VALUES('Angola','Africa',1246700,20609294,100990000);
```


**#打卡规则#**

学员微信群编号+姓名+CSDN博客或Github链接。

  * 比如我是0号，打卡内容就是 000+杨煜+https://github.com/magicyang5000/
# 【任务说明】

1.1 是软件安装和配置，以及一些数据库理论知识储备。

1.2 是最最基础的查询语句，可以说学完本次课程，SQL语句就掌握了30%了。

语言规范非常重要，请大家认真仔细阅读。请记住，你写SQL需要考虑别人review时的心情。写的过于杂乱会分分钟造成暴力事件。

学习内容中函数部分，是让大家了解下MySQL可以怎样处理一些数据。了解些常用的，等实际中遇到了再回头查找详细就行。



学习内容是指需要在博客文章中总结的知识点，包括但不仅限于这些知识点。比如一些安装过程中的报错及解决办法也可以写。

祝大家学习开心。:-)
