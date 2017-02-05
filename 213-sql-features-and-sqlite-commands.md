**2.1.3 SQL特性与命令**

SQLite支持ANSI SQL-92定义的一部分SQL语言，同时实现了一些SQLite特有的命令。你可以使用标准的数据定义语言创建表，索引，触发器，和视图。你可以使用INSERT,DELETE,UPDATE与SELECT等语言存储和维护数据信息。下面将列出SQLite所支持的一些SQL语言（以SQLite 3.8.9为例）：



1. 数据定义语言，DDL

a. Create：创建表格，索引，视图与触发器

b. Delete：删除表格，索引，视图与触发器

c. Alter Table：修改表名表名与增加列

d. UNIQUE, NOT NULL, 与CHECK约束

e. 主键，外键

f. 自增，列比较规则

g. 冲突解决

2. 数据操作语言：DML

a. INSERT, DELETE, UPDATE, 与SELECT

b. 子查询

c. Group by，order by，limit，collation

d. INNER JOIN, LEFT OUTER JOIN, NATURAL JOIN

e. UNION, UNION ALL, INTERSECT, EXCEPT

f. 参数命名与数据绑定

g. 行级别的触发器

  


3. 事务命令

a. BEGIN

b. COMMIT

c. ROLLBACK

d. SAVEPOINT

e. RELEASE

f. ROLLBACK TO  


PS. BEGIN无法嵌套

![](evernotecid://6290FB03-6E65-4218-9E07-51CE5DB6CD91/ENResource/p1110)

  


![](evernotecid://6290FB03-6E65-4218-9E07-51CE5DB6CD91/ENResource/p1108)

  


  


4. SQLite特有的命令：

a. reindex

b. attach，detach

c. explain

d. pragma

