---
title: MySQL practise
date: 2021-11-17
author: wflin
tags:
  - MySQL
top: false
cover: false
toc: true
mathjax: true
summary: "springboot"
categories:
  - java
---

# MySQL exam

[传送门]([牛客网 - 找工作神器|笔试题库|面试经验|实习招聘内推，求职就业一站解决_牛客网 (nowcoder.com)](https://www.nowcoder.com/exam/oj?tab=SQL篇&topicId=199&fromPut=pc_wzcpa_yupi_sf))

## 创建数据库并插入数据

### SQL Select 语句完整的执行顺序

````markdown

1、from 子句组装来自不同数据源的数据；
2、where 子句基于指定的条件对记录行进行筛选； 
3、group by 子句将数据划分为多个分组； 
4、使用聚集函数进行计算； 
5、使用 having 子句筛选分组； 
6、计算所有的表达式； 
7、select 的字段； 
8、使用 order by 对结果集进行排序。

SQL 语言不同于其他编程语言的最明显特征是处理代码的顺序。 在大多数据库语言中，代码按编码顺序被处理。但在 SQL 语句中，第一个被处理的子句式 FROM，而不是第一出现的 SELECT。
````

### 语句顺序展示

```txt
(1) FROM <left_table> 
(2) <join_type> JOIN <right_table> 
(3) ON <join_condition> 
(4) WHERE <where_condition> 
(5) GROUP BY <group_by_list>
(6) WITH {CUBE | ROLLUP} 
(7) HAVING <having_condition> 
(8) SELECT (9) DISTINCT 
(9) ORDER BY <order_by_list> 
(10) <TOP_specification> <select_list>
```

## 要求返回结果的形式

### 第一题

* 样式

  ```txt
  1	2138	male	21	北京大学	BeiJing
  2	3214	male		复旦大学	Shanghai
  3	6543	female	20	北京大学	BeiJing
  4	2315	female	23	浙江大学	ZheJiang
  5	5432	male	25	山东大学	Shandong
  ```

  

* 题解

  ```sql
  -- 牛客第一题
  select
  	id,device_id,gender,age,university,province 
  from 
  	user_profile
  ```

  

### 第二题

* 样式

  ```txt
  2138	male	21	北京大学
  3214	male		复旦大学
  6543	female	20	北京大学
  2315	female	23	浙江大学
  5432	male	25	山东大学
  ```

* 题解

  ```sql
  -- 牛客第二题
  select 
  	device_id,gender,age,university 
  from 
  	user_profile
  ```

  

### 第三题（结果去重）

* 样式

  ```txt
  北京大学
  复旦大学
  浙江大学
  山东大学
  ```

  

* 题解

  * **distinct** 去掉查询结果的重复数据

  ```sql
  
  -- 牛客第三题,去重
  -- distinct 去掉查询结果的重复数据
  SELECT distinct 
  	university 
  FROM 
  	user_profile
  ```

### 第三题（limit 关键字考察）

* 样式

  ```txt
  device_id
  2138
  3214
  ```

* 题解

  * **limit** 展示查询结果的前多少数据，多用于分页查询

  ```sql
  select 
  	device_id 
  from 
  	user_profile 
  limit 2
  ```

### 第四题（as）关键字考察

* 样式

  ```txt
  user_infos_example
  2138
  3214
  ```

  

* 题解

  * **as**  给数据字段起别名

  ```sql
  select device_id user_infos_example from user_profile limit 2
  ```

### 第五题（where ）精确查询

* 样式

  ```txt
  device_id university
    2138	   北京大学
    6543	   北京大学
  ```

* 题解

  * **where** 连接查询条件

  ```sql
  select device_id,university from user_profile where university="北京大学"
  ```

### 第六题（where）条件查询

* 要求

  >**查找年龄大于24岁的用户信息**

* 样式

  ```txt
  device_id	gender	age	university
  5432	male	25	山东大学
  ```

  

* 题解

  ```sql
  select device_id,gender,age,university from user_profile where age>24
  ```

### 第七题（where，between and）区间查询

* 要求

  >现在运营想要针对20岁及以上且23岁及以下的用户开展分析，请你取出满足条件的设备ID、性别、年龄。
  >
  >用户信息表：user_profile

* 样式

  ```txt
  
  device_id	gender	age
  2138	male	21
  6543	female	20
  2315	female	23
  ```

* 题解
  * 解法一

  ```sql
  select device_id,gender,age from user_profile where age<=23
  ```

  

  * 解法二

  ```sql
  select device_id,gender,age from user_profile where age between 20 and 23
  ```

### 第八题（>=<, !=,<>）逻辑词考察

* 要求

  > 查找除复旦大学的用户信息

* 样式

  ```ttx
  device_id	gender	age	university
  2138	male	21	北京大学
  6543	female	20	北京大学
  2315	female	23	浙江大学
  5432	male	25	山东大学
  ```

  

* 题解

  * 解法一

  ```sql
  select device_id,gender,age,university from user_profile where university<>'复旦大学'
  ```

  * 解法二

  ```sql
  select device_id,gender,age,university from user_profile where university != '复旦大学'
  ```

  * 解法三

  ```sql
  select device_id,gender,age,university from user_profile where university not in ('复旦大学')
  ```

  * [ NOT ] IN (‘常量1’ , ‘常量2’ , ‘常量3’ , ‘常量4’)

    * IN：当列表中的值于IN中的某个值相等时，则结果为 True，表明此记录为符合查询条件的记录
    * NOT IN：当列中的值与某个常量值相等时，结果为False，表明此纪录为不符合查询条件的记录。

  * 例子：查询所有班名是“电子信息工程技术”、“电子声像”或“电子组装技术”的班号和班名。

    ```sql
    SELECT 班号, 班名 FROM 班级表
    WHERE 班名 IN ('电子信息工程技术' , '电子声像‘, '电子组装技术')
    ```

  * 等价于：

    ```sql
    SELECT 班号,班名 FROM 班级表
    WHERE 班名= ‘电子信息工程技术’ OR
    班名 = ‘电子声像’ OR
    班名 = ‘电子组装技术’
    ```

### 第九题（is not、is）

* 要求

  > 用 where 过滤空值练习

* 样式

  ```txt
  device_id	gender	age	university
  2138	male	21	北京大学
  6543	female	20	北京大学
  2315	female	23	浙江大学
  5432	male	25	山东大学
  ```

* 题解

  * 题解一

  ```sql
  select 
  	device_id,gender,age,university 
  from 
  	user_profile 
  where 
  	age is not null
  ```

  * 题解二

  ```sql
  
  select device_id,gender,age,university
  from user_profile
  where age !='';
  ```


### 字符匹配

* ### 

  一般形式为：

  列名 [NOT ] LIKE

  匹配串中可包含如下四种通配符：
  _：匹配任意一个字符；
  %：匹配0个或多个字符；
  [ ]：匹配[ ]中的任意一个字符(若要比较的字符是连续的，则可以用连字符“-”表 达 )；
  [^ ]：不匹配[ ]中的任意一个字符。

  例23．查询学生表中姓‘张’的学生的详细信息。

  ```SQL
  SELECT` `* ``FROM` `学生表 ``WHERE` `姓名 ``LIKE` `‘张%’
  ```

  例24．查询姓“张”且名字是3个字的学生姓名。

  ```SQL
  SELECT` `* ``FROM` `学生表 ``WHERE` `姓名 ``LIKE` `'张__’
  ```

  如果把姓名列的类型改为nchar(20)，在SQL Server 2012中执行没有结果。原因是姓名列的类型是char(20)，当姓名少于20个汉字时，系统在存储这些数据时自动在后边补空格，空格作为一个字符，也参加LIKE的比较。可以用rtrim()去掉右空格。

  ```SQL
  SELECT` `* ``FROM` `学生表 ``WHERE` `rtrim(姓名) ``LIKE` `'张__'
  ```

  例25.查询学生表中姓‘张’、姓‘李’和姓‘刘’的学生的情况。

  ```SQL
  SELECT` `* ``FROM` `学生表 ``WHERE` `姓名 ``LIKE` `'[张李刘]%’
  ```

  例26.查询学生表表中名字的第2个字为“小”或“大”的学生的姓名和学号。

  ```SQL
  SELECT` `姓名,学号 ``FROM` `学生表 ``WHERE` `姓名 ``LIKE` `'_[小大]%'
  ```

  例27.查询学生表中所有不姓“刘”的学生。

  ```SQL
  SELECT` `姓名 ``FROM` `学生 ``WHERE` `姓名 ``NOT` `LIKE` `'刘%’
  ```

  例28.从学生表表中查询学号的最后一位不是2、3、5的学生信息。

  [复制代码](https://www.nowcoder.com/practice/95d9922b1e2a49de80daa491889969ee?tpId=199&tqId=1971219&ru=%2Fpractice%2Ff9f82607cac44099a77154a80266234a&qru=%2Fta%2Fsql-quick-study%2Fquestion-ranking&sourceUrl=%2Fexam%2Foj%3Ftab%3DSQL%E7%AF%87%26topicId%3D199%26fromPut%3Dpc_wzcpa_yupi_sf#)

  ```SQL
  SELECT` `* ``FROM` `学生表 ``WHERE` `学号 ``LIKE` `'%[^235]'
  ```

### 第十题（max）

* 找出复旦大学 gpa 最高是多少

  * 解法一：

    ```sql
    # 方法1
    select max(gpa) as gpa
    from user_profile
    where university='复旦大学';
    ```

  * 解法二

    ```sql
    # 方法2
    select gpa
    from user_profile
    where university='复旦大学'
    order by gpa desc limit 1
    ```

### 第十一题（avg，round）

* 现在运营想要看一下男性用户有多少人以及他们的平均gpa是多少，用以辅助设计相关活动，请你取出相应数据。

* **avg**：平均值

* **round**：保留小数

  * 解法：

    ```sql
    select count(1) as male_num,gpa as avg_gpa where user_profile where gender='male'
    ```

### 第十二题（group by）

* 题目：现在运营想要对每个学校不同性别的用户活跃情况和发帖数量进行分析，请分别计算出每个学校每种性别的用户数、30天内平均活跃天数和平均发帖数量。

* 信息：

  ```txt
  用户信息表：user_profile
  30天内活跃天数字段（active_days_within_30）
  发帖数量字段（question_cnt）
  回答数量字段（answer_cnt）
  ```

* 表信息说明

  ```txt
  第一行表示:id为1的用户的常用信息为使用的设备id为2138，性别为男，年龄21岁，北京大学，gpa为3.4在过去的30天里面活跃了7天，发帖数量为2，回答数量为12
  。。。
  最后一行表示:id为7的用户的常用信息为使用的设备id为4321，性别为男，年龄26岁，复旦大学，gpa为3.6在过去的30天里面活跃了9天，发帖数量为6，回答数量为52
  ```

* **group by**：按照字段分组，可以是多个字段

  * 解法

    ```sql
    select 
        gender,university,
        count(device_id) as user_num,
        avg(active_days_within_30) as avg_active_day,
        avg(question_cnt) as avg_question_cnt
    from user_profile
    GROUP by university,gender
    ```

    

### 第十九题

* 题目：现在运营想查看每个学校用户的平均发贴和回帖情况，寻找低活跃度学校进行重点运营，请取出平均发贴数低于5的学校或平均回帖数小于20的学校。

* 表格描述

  ```txt
  第一行表示:id为1的用户的常用信息为使用的设备id为2138，性别为男，年龄21岁，北京大学，gpa为3.4在过去的30天里面活跃了7天，发帖数量为2，回答数量为12
  。。。
  最后一行表示:id为7的用户的常用信息为使用的设备id为4321，性别为男，年龄26岁，复旦大学，gpa为3.6在过去的30天里面活跃了9天，发帖数量为6，回答数量为52
  
  根据示例，你的查询应返回以下结果，请你保留3位小数(系统后台也会自动校正)，3位之后四舍五入：
  ```

* 表信息说明

  ```txt
  university	avg_question_cnt	avg_answer_cnt
  北京大学	2.5000	21.000
  浙江大学	1.000	2.000
  ```

* **问题分解**

  * 限定条件：平均发贴数低于5或平均回帖数小于20的学校，`avg(question_cnt)<5 or avg(answer_cnt)<20`，聚合函数结果作为筛选条件时，不能用where，而是用having语法，配合重命名即可；
  * 按学校输出：需要对每个学校统计其平均发贴数和平均回帖数，因此`group by university`

* **问题细节**

  * having ，对查询结果进行过滤，过滤不能用  `过滤计算函数之后的结果不能用 where 这里`。

* 题解

  ```sql
  select
      university,
      avg(question_cnt) as avg_question_cnt,
      avg(answer_cnt) as avg_answer_cnt
  from user_profile
  group by university
  having avg_question_cnt<5 or avg_answer_cnt<20
  ```



### 第 二十 题 （order by）

* **题目**

  现在运营想要查看不同大学的用户平均发帖情况，并期望结果按照平均发帖情况进行升序排列，请你取出相应数据。

* 描述

  ```txt
  第一行表示:id为1的用户的常用信息为使用的设备id为2138，性别为男，年龄21岁，北京大学，gpa为3.4在过去的30天里面活跃了7天，发帖数量为2，回答数量为12
  。。。
  最后一行表示:id为7的用户的常用信息为使用的设备id为4321，性别为男，年龄26岁，复旦大学，gpa为3.6在过去的30天里面活跃了9天，发帖数量为6，回答数量为52
  ```

* 查询结果

  ```txt
  university	avg_question_cnt
  浙江大学	1.0000
  北京大学	2.5000
  复旦大学	5.5000
  山东大学	11.0000
  ```

* 题解

  * order by 字段名 `desc` 和 `asc`
    * **desc** 降序排列
    * **asc** 升序排列（查询结果默认也是升序排列） 

  ```sql
  select university, avg(question_cnt)  avg_question_cnt
  from user_profile
  order by desc
  group by university
  ```

### 第二十一题（子查询）

* **题目**

  现在运营想要查看所有来自浙江大学的用户题目回答明细情况，请你取出相应数据

* **描述**

  ```txt
  第一行表示:id为1的用户的常用信息为使用的设备id为2138，性别为男，年龄21岁，北京大学，gpa为3.4在过去的30天里面活跃了7天，发帖数量为2，回答数量为12
  。。。
  最后一行表示:id为7的用户的常用信息为使用的设备id为4321，性别为男，年龄26岁，复旦大学，gpa为3.6在过去的30天里面活跃了9天，发帖数量为6，回答数量为52
  ```

  

* **显示信息说明**

  ![img](%E7%89%9B%E5%AE%A2MySQL/D9E601E7A15464E123E07993B5B38512.png)

* **题解**

  * 解法一：子查询

    * 在 where 的位置使用 子查询 

    ```sql
    SELECT
    device_id,
    question_id,
    result
    FROM question_practice_detail
    WHERE device_id IN (SELECT device_id FROM
    user_profile
    WHERE university ='浙江大学');
    ```

    

  * 解法二：连接查询

    * 连接查询
      * 默认是 `内连接`
      * `外连接` （左连接，右连接）

    ```sql
    select qpd.device_id, qpd.question_id, qpd.result
    from question_practice_detail as qpd
    inner join user_profile as up
    on up.device_id=qpd.device_id and up.university='浙江大学'
    order by question_id
    ```

  * 两个查询的速率这个题目而言，子查询的速度快（具体谁快，等哈）

### 第二十二（连接查询）

* **题目**

  运营想要了解每个学校答过题的用户平均答题数量情况，请你取出数据

  [传送门]((1) FROM <left_table>  (2) <join_type> JOIN <right_table>  (3) ON <join_condition>  (4) WHERE <where_condition>  (5) GROUP BY <group_by_list> (6) WITH {CUBE | ROLLUP}  (7) HAVING <having_condition>  (8) SELECT (9) DISTINCT  (9) ORDER BY <order_by_list>  (10) <TOP_specification> <select_list>)

* **描述**

  ```txt
  第一行表示:用户的常用信息为使用的设备id为2138，性别为男，年龄21岁，北京大学，gpa为3.4，在过去的30天里面活跃了7天
  最后一行表示:用户的常用信息为使用的设备id为4321，性别为男，年龄28岁，复旦大学，gpa为3.6，在过去的30天里面活跃了9天
  ```

* **显示说明**

* 请你写SQL查找每个学校用户的平均答题数目(说明：某学校用户平均答题数量计算方式为该学校用户答题总次数除以答过题的不同用户个数)根据示例，你的查询应返回以下结果（结果保留4位小数），注意：**结果按照university升序排序！！！**

  | university | avg_answer_cnt |
  | ---------- | -------------- |
  | 北京大学   | 1.0000         |
  | 复旦大学   | 2.0000         |
  | 山东大学   | 2.0000         |
  | 浙江大学   | 3.0000         |

* **题解**

  ```sql
  select 
  	university,
      count(question_id) / count(distinct qpd.device_id) as avg_answer_cnt
  from 
  	question_practice_detail as qpd
  inner join 
  	user_profile as up
  on 
  	qpd.device_id=up.device_id
  group by 
  	university
  ```

  

### 第二十三（三表查询，注意去重）

* [传送门](https://www.nowcoder.com/questionTerminal/5400df085a034f88b2e17941ab338ee8)

* 题解 

  统计每个学校各难度的用户平均刷题数

* 题意明确

  计算每个学校用户不同难度下的用户平均答题题目数

* 问题分解：
  * 限定条件：无；
  * 每个学校：按学校分组`group by university`
  * 不同难度：按难度分组`group by difficult_level`
  * 平均答题数：总答题数除以总人数`count(qpd.question_id) / count(distinct qpd.device_id)`
  * 来自上面信息三个表，需要联表，up与qpd用device_id连接，qd与qpd用question_id连接。

* 细节问题：
  * 表头重命名：as
  * 平均值精度：保留4位小数round(x, 4)

* 代码

  * 解法一（左连接）

  ```sql
  select 
      university,
      difficult_level,
      round(count(qpd.question_id) / count(distinct qpd.device_id), 4) as avg_answer_cnt
  from question_practice_detail as qpd
  
  left join user_profile as up
  on up.device_id=qpd.device_id
  
  left join question_detail as qd
  on qd.question_id=qpd.question_id
  
  group by university, difficult_level
  ```

  

  * 解法二（内连接）

  ```sql
  select up.university,qd.difficult_level,(count(1)/count(distinct(qpd.device_id))) 
  from user_profile up 
  join question_practice_detail qpd on up.device_id = qpd.device_id
  join question_detail qd on qd.question_id =qpd.question_id
  GROUP by up.university,qd.difficult_level
  ```


### 第二十四

* [传送门]([统计每个用户的平均刷题数_牛客题霸_牛客网 (nowcoder.com)](https://www.nowcoder.com/practice/f4714f7529404679b7f8909c96299ac4?tpId=199&tqId=1971219&ru=%2Fpractice%2Ff9f82607cac44099a77154a80266234a&qru=%2Fta%2Fsql-quick-study%2Fquestion-ranking&sourceUrl=%2Fexam%2Foj%3Ftab%3DSQL%E7%AF%87%26topicId%3D199%26fromPut%3Dpc_wzcpa_yupi_sf))

* 题解

  统计每个用户的平均刷题数

* 题意明确

  

* 问题分解

* 细节问题

* 代码