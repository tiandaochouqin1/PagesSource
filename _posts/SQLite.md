---
title: SQLite
tags:
  - [数据库]
  - [Python]
categories: [Web]
date: 2020-08-10 22:37:23
---
<font face="微软雅黑"> </font>
<center> </center>

<!-- more -->
[MySQL](http://gitbook.net/mysql/index.html)

[MySql 三大知识点——索引、锁、事务](https://juejin.cn/post/6844903802173128718)

[SQLite - Python](https://www.runoob.com/sqlite/sqlite-python.html)
# python3接口
```python
import sqlite3
conn = sqlite3.connect('sq.db')
cursor = conn.cursor()
cursor.execute('select * fromsqlite_master')
values = cursor.fetchall()
values


cursor.close()
conn.close()
```

sqlite 中有一个内建表 `sqlite_master`，这个表中存储这所有自建表的表名称等信息。
查询 sqlite 中所有表：
```
select name from sqlite_master where type='table' order by name;

```
查看这个内建表的所有记录：
```
select * from sqlite_master;
```
其它命令
```
connection.commit()  //提交更改

cursor.execute("insert into people values (?, ?)", (who, age))  //参数化sql命令
```

# SQLite语法
SQLite 是不区分大小写的。
1. 创建数据库
```
.databases

sqlite3 DatabaseName.db
```
2. 附加数据库
选择一个特定的数据库。
```
ATTACH DATABASE file_name AS database_name;
```
3. 分离数据库
```
DETACH DATABASE 'Alias-Name';
```
4. 创建表
创建基本表，涉及到命名表、定义列及每一列的数据类型。
```
.tables

CREATE TABLE database_name.table_name(
   column1 datatype  PRIMARY KEY(one or more columns),
   column2 datatype,
   column3 datatype,
   .....
   columnN datatype,
);
```
5. Insert语句
指定列和值：
```
INSERT INTO TABLE_NAME [(column1, column2, column3,...columnN)]  
VALUES (value1, value2, value3,...valueN);
```
为所有列添加时，可省略列，值需保持顺序一致：
```
INSERT INTO TABLE_NAME VALUES (value1,value2,value3,...valueN);
```
6. Where
指定从一个表或多个表中获取数据的条件。
```
SELECT column1, column2, columnN 
FROM table_name
WHERE [condition]
```
7. Update
```
UPDATE table_name
SET column1 = value1, column2 = value2...., columnN = valueN
WHERE [condition];
```
8. Delete
```
DELETE FROM table_name
WHERE [condition];
```
9. Like:`%`——零或多个字符或数字；`_`——单个字符或数字。
10. Glob:`*`——零或多个字符或数字；`?`——单个字符或数字。大小写敏感
11. Limit
指定Select语句返回的数据数量
```
SELECT * FROM COMPANY LIMIT 3 OFFSET 2;
```
12. Order By
按照升序/降序排列。
```
SELECT column-list 
FROM table_name 
[WHERE condition] 
[ORDER BY column1, column2, .. columnN] [ASC | DESC];
```
13. Group By
对相同数据进行分组。
GROUP BY 子句必须放在 WHERE 子句中的条件之后，必须放在 ORDER BY 子句之前。

```
SELECT column-list
FROM table_name
WHERE [ conditions ]
GROUP BY column1, column2....columnN
ORDER BY column1, column2....columnN
```
用于计算求和：
```
SELECT NAME, SUM(SALARY) FROM COMPANY GROUP BY NAME ORDER BY NAME;
```
14. Having
过滤结果。位置如下：
```
SELECT
FROM
WHERE
GROUP BY
HAVING
ORDER BY
```
15. Distinct
消除重复记录：
```
SELECT DISTINCT column1, column2,.....columnN 
FROM table_name
WHERE [condition]

```