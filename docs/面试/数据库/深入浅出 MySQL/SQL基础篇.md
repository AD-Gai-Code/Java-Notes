#  1. SQL 语法基础

##  1.1 SQL 分类

SQL 语句主要可以划分为以下 3 个类别。

* DDL（Data Definition Languages）语句：数据定义语言，这些语句定义了不同的数据段、数据库、表、列、索引等数据库对象的定义。常用的语句关键字主要包括 `create`、`drop`、`alter` 等。
* DML（Data Manipulation Language）语句：数据操纵语句，用于添加、删除、更新和查询数据库记录，并检查数据完整性，常用的语句关键字主要包括 `insert`、`delete`、`udpate` 和 `select` 等。
* DCL（Data Control Language）语句：数据控制语句，用于控制不同数据段直接的许可和访问级别的语句。这些语句定义了数据库、表、字段、用户的访问权限和安全级别。主要的语句关键字包括 grant、revoke 等。

##  1.2 DDL 语句

`DDL` 是数据定义语言的缩写，简单来说，就是对数据库内部的对象进行创建、删除、修改的操作语言。`DDL` 语句更多的被数据库管理员（DBA）所使用， 一般的开发人员很少使用。

###  1.2.1 创建数据库

```mysql
mysql> create database test1;
Query OK, 1 row affected (0.00 sec)
```

如果已经存在这个数据库，系统会提示：

```mysql
mysql> create database test1;
ERROR 1007 (HY000): Can't create database 'test1'; database exists
```

令选择要操作的数据库：

```mysql
mysql> use test1
Database changed
```

然后再用以下命令来查看 test1 数据库中创建的所有数据表：

```mysql
mysql> show tables;
Empty set (0.00 sec)
```

###  1.2.2 删除数据库

要删除 test1 数据库可以使用以下语句：

```mysql
mysql> drop database test1;
Query OK, 0 rows affected (0.00 sec)
```

###  1.2.3 创建表

在数据库中创建一张表的基本语法如下：

```mysql
CREATE TABLE tablename (column_name_1 column_type_1 constraints，
column_name_2 column_type_2 constraints , 
……column_name_n column_type_n 
constraints）
```

因为 MySQL 的表名是以目录的形式存在于磁盘上，所以表名的字符可以用任何目录名允许的字符。column_name 是列的名字，column_type 是列的数据类型，contraints 是这个列的约束条件。

```mysql
mysql> create table emp(ename varchar(10),hiredate date,sal decimal(10,2),deptno int(2));
Query OK, 0 rows affected (0.02 sec)
```

###  1.2.4 删除表

```mysql
mysql> drop table emp;
Query OK, 0 rows affected (0.00 sec)
```

###  1.2.5 修改表

1. 修改数据类型

	例如，修改表 emp 的 ename 字段定义，将 varchar(10)改为 varchar(20)：

	```mysql
	mysql> alter table emp modify ename varchar(20);
	Query OK, 0 rows affected (0.03 sec)
	```

2. 增加表字段

	例如，表 emp 上新增加字段 age，类型为 int(3)：

	```mysql
	mysql> alter table emp add column age int(3);
	Query OK, 0 rows affected (0.03 sec)
	```

3. 删除表字段

	例如，将字段 age 删除掉：

	```mysql
	mysql> alter table emp drop column age;
	Query OK, 0 rows affected (0.04 sec)
	```

4. 字段改名

	```mysql
	ALTER TABLE tablename CHANGE [COLUMN] old_col_name column_definition
	[FIRST|AFTER col_name]
	// 将 age 字段改为 age1
	mysql> alter table emp change age age1 int(4) ;
	Query OK, 0 rows affected (0.02 sec)
	```

	**注意：`change` 和 `modify` 都可以修改表的定义，不同的是 `change` 后面需要写两次列名，不方便。 但是 `change` 的优点是可以修改列名称，`modify` 则不能。**

5. 修改字段排列顺序

	字段增加和修改语法（ADD/CNAHGE/MODIFY）中，都有一个可选项 `first|after  column_name`，这个选项可以用来修改字段在表中的位置，默认 `ADD` 增加的新字段是加在表的最后位置，而 `CHANGE/MODIFY` 默认都不会改变字段的位置。 例如，将新增的字段 `birth date` 加在 `ename` 之后：

	```mysql
	mysql> alter table emp add birth date after ename;
	Query OK, 0 rows affected (0.03 sec)
	```

	修改字段 age，将它放在最前面：

	```mysql
	mysql> alter table emp modify age int(3) first;
	Query OK, 0 rows affected (0.03 sec)
	```

6. 表改名

	例如，将表 emp 改名为 emp1:

	```mysql
	mysql> alter table emp rename emp1;
	Query OK, 0 rows affected (0.00 sec)
	```

##  1.3 DML 语句

DML 操作是指对数据库中表记录的操作，主要包括表记录的插入`insert`、更新`update`、删除`delete`和查询`select`，是开发人员日常使用最频繁的操作。

###  1.3.1 插入记录

例如，向表 `emp` 中插入以下记录：`ename` 为 `zzx1`，`hiredate` 为 `2000-01-01，sal` 为 `2000`，`deptno` 为 1：

```mysql
mysql> insert into emp (ename,hiredate,sal,deptno) values('zzx1','2000-01-01','2000',1);
Query OK, 1 row affected (0.00 sec)
```

也可以不用指定字段名称，但是 values 后面的顺序应该和字段的排列顺序一致：

```mysql
mysql> insert into emp values('lisa','2003-02-01','3000',2);
Query OK, 1 row affected (0.00 sec)
```

对于含可空字段、非空但是含有默认值的字段、自增字段，可以不用在 insert 后的字段列表 里面出现，values 后面只写对应字段名称的 value，这些没写的字段可以自动设置为 NULL、 默认值、自增的下一个数字，这样在某些情况下可以大大缩短 SQL 语句的复杂性。 例如，只对表中的 ename 和 sal 字段显式插入值：

```mysql
mysql> insert into emp (ename,sal) values('dony',1000);
Query OK, 1 row affected (0.00 sec)
```

在 MySQL 中，insert 语句还有一个很好的特性，可以一次性插入多条记录:

```mysql
INSERT INTO tablename (field1, field2,……fieldn)
VALUES
(record1_value1, record1_value2,……record1_valuesn),
(record2_value1, record2_value2,……record2_valuesn),
……
(recordn_value1, recordn_value2,……recordn_valuesn)
;
```

每条记录之间都用逗号进行了分隔。

###  1.3.2 更新记录

例如，将表 emp 中 ename 为“lisa”的薪水（sal）从 3000 更改为 4000：

```mysql
mysql> update emp set sal=4000 where ename='lisa';
Query OK, 1 row affected (0.00 sec)
```

###  1.3.3 删除记录

```mysql
DELETE FROM tablename [WHERE CONDITION]
```

例如，在 `emp` 中将 `ename` 为`dony`的记录全部删除:

```mysql
mysql> delete from emp where ename='dony';
Query OK, 1 row affected (0.00 sec)
```

###  1.3.4 查询记录

```mysql
SELECT * FROM tablename [WHERE CONDITION]
```

####  （1）查询不重复的记录

有时需要将表中的记录去掉重复后显示出来，可以用 distinct 关键字来实现：

![image-20211029112432515](https://gitee.com/AD-Gai-Code/pic-go/raw/master/202110291124210.png)

####  （2）条件查询

在很多情况下，用户并不需要查询所有的记录，而只是需要根据限定条件来查询一部分数据， 用 `where` 关键字可以来实现这样的操作。 例如，需要查询所有 `deptno` 为 1 的记录：

![image-20211029112848497](https://gitee.com/AD-Gai-Code/pic-go/raw/master/202110291128599.png)

结果集中将符合条件的记录列出来。上面的例子中，`where` 后面的条件是一个字段的`=` 比较，除了`=`外，还可以使用`>、<、>=、<=、!=`等比较运算符；多个条件之间还可以使 用 `or`、`and` 等逻辑运算符进行多条件联合查询。

![image-20211029113123408](https://gitee.com/AD-Gai-Code/pic-go/raw/master/202110291131321.png)

####  （3）排序和限制

我们经常会有这样的需求，取出按照某个字段进行排序后的记录结果集，这就用到了数据库 的排序操作，用关键字 `ORDER BY` 来实现，语法如下：

```mysql
SELECT * FROM tablename [WHERE CONDITION] [ORDER BY field1 [DESC|ASC] ， field2 
[DESC|ASC]，……fieldn [DESC|ASC]]
```

其中，`DESC` 和 `ASC` 是排序顺序关键字，`DESC` 表示按照字段进行降序排列，`ASC` 则表示升序排列，如果不写此关键字默认是升序排列。`ORDER BY `后面可以跟多个不同的排序字段，并且每个排序字段可以有不同的排序顺序。 例如，把 `emp` 表中的记录按照工资高低进行显示：

![image-20211029113702654](https://gitee.com/AD-Gai-Code/pic-go/raw/master/202110291137618.png)

如果排序字段的值一样，则值相同的字段按照第二个排序字段进行排序，以此类推。如果只有一个排序字段，则这些字段相同的记录将会无序排列。 

例如，把 `emp` 表中的记录按照部门编号 `deptno` 字段排序：

![image-20211029113818895](https://gitee.com/AD-Gai-Code/pic-go/raw/master/202110291138607.png)

对于 deptno 相同的前两条记录，如果要按照工资由高到低排序，可以使用以下命令：

![image-20211029113902609](https://gitee.com/AD-Gai-Code/pic-go/raw/master/202110291139540.png)

对于排序后的记录，如果希望只显示一部分，而不是全部，这时，就可以使用 `LIMIT` 关键字 来实现，`LIMIT` 的语法如下：

```mysql
SELECT ……[LIMIT offset_start,row_count]
```

**其中 `offset_start` 表示记录的起始偏移量，`row_count` 表示显示的行数**。 在默认情况下，起始偏移量为 0，只需要写记录行数就可以，这时候，显示的实际就是前 n 条记录，看下面例子： 

例如，显示 `emp` 表中按照 `sal` 排序后的前 3 条记录：

![image-20211029114432256](https://gitee.com/AD-Gai-Code/pic-go/raw/master/202110291144384.png)

如果要显示 `emp` 表中按照 `sal` 排序后从第二条记录开始，显示 3 条记录：

![image-20211029114545785](https://gitee.com/AD-Gai-Code/pic-go/raw/master/202110291145896.png)

#### （4）聚合

很多情况下，我们需要进行一些汇总操作，比如统计整个公司的人数或者统计每个部门的人数，这个时就要用到 `SQL` 的聚合操作。

```mysql
SELECT [field1,field2,……fieldn] fun_name 
FROM tablename
[WHERE where_contition]
[GROUP BY field1,field2,……fieldn
[WITH ROLLUP]]
[HAVING where_contition]
```

* `fun_name` 表示要做的聚合操作，也就是聚合函数，常用的有 `sum`(求和)、`count(*)`(记录数)、`max`(最大值)、`min`(最小值)
* `GROUP BY` 关键字表示要进行分类聚合的字段，比如要按照部门分类统计员工数量，部门就应该写在 `group by` 后面。 
* `WITH ROLLUP` 是可选语法，表明是否对分类聚合后的结果进行再汇总。
* `HAVING` 关键字表示对分类后的结果再进行条件的过滤。

**注意：`having` 和 `where` 的区别在于 `having` 是对聚合后的结果进行条件的过滤，而 `where` 是在聚合前就对记录进行过滤，如果逻辑允许，我们尽可能用 `where` 先过滤记录，这样因为结果集减小，将对聚合的效率大大提高，最后再根据逻辑看是否用 `having` 进行再过滤。**

例如，要 emp 表中统计公司的总人数：

![image-20211029115656443](https://gitee.com/AD-Gai-Code/pic-go/raw/master/202110291156328.png)

在此基础上，要统计各个部门的人数：

![image-20211029115711969](https://gitee.com/AD-Gai-Code/pic-go/raw/master/202110291157703.png)

更细一些，既要统计各部门人数，又要统计总人数：

![image-20211029115827391](https://gitee.com/AD-Gai-Code/pic-go/raw/master/202110291158487.png)

![image-20211029115840840](https://gitee.com/AD-Gai-Code/pic-go/raw/master/202110291158222.png)

统计人数大于 1 人的部门：

![image-20211029120052239](https://gitee.com/AD-Gai-Code/pic-go/raw/master/202110291200279.png)

最后统计公司所有员工的薪水总额、最高和最低薪水：

![image-20211029155058208](https://gitee.com/AD-Gai-Code/pic-go/raw/master/202110291551159.png)

####  （5）表连接

从大类上分，表连接分为内连接和外连接，它们之间的最主要区别是內连接仅选出两张表中互相匹配的记录，而外连接会选出其他不匹配的记录。我们最常用的是内连接。

外连接有分为左连接和右连接，具体定义如下。 

* 左连接：包含所有的左边表中的记录甚至是右边表中没有和它匹配的记录
* 右连接：包含所有的右边表中的记录甚至是左边表中没有和它匹配的记录

![img](https://gitee.com/AD-Gai-Code/pic-go/raw/master/202110291624299.jpeg)

可参考[175. 组合两个表](https://leetcode-cn.com/problems/combine-two-tables/submissions/)

####  （6）子查询

某些情况下，当我们查询的时候，需要的条件是另外一个 select 语句的结果，这个时候，就 要用到子查询。用于子查询的关键字主要包括 `in、not in、=、!=、exists、not exists` 等。

例如，从 emp 表中查询出所有部门在 dept 表中的所有记录：

![image-20211029162828590](https://gitee.com/AD-Gai-Code/pic-go/raw/master/202110291628496.png)

####  （7）记录联合

我们经常会碰到这样的应用，将两个表的数据按照一定的查询条件查询出来后，将结果合并到一起显示出来，这个时候，就需要用 `union` 和 `union all` 关键字来实现这样的功能，具体语法如下：

```mysql
SELECT * FROM t1
UNION|UNION ALL
SELECT * FROM t2
……
UNION|UNION ALL
SELECT * FROM tn;
```

`UNION` 和 `UNION ALL` 的主要区别是 `UNION ALL` 是把结果集直接合并在一起，而 `UNION` 是将 `UNION ALL` 后的结果进行一次 `DISTINCT`，去除重复记录后的结果。

##  1.4 DCL 语句

DCL 语句主要是 DBA 用来管理系统中的对象权限时所使用，一般的开发人员很少使用。具体细节后面碰到再说。

#  2. SQL运算符

[MySQL 教程 | 菜鸟教程 (runoob.com)](https://www.runoob.com/mysql/mysql-tutorial.html)

`LIKE`运算符的使用格式为`a LIKE %123%`,当 a 中含有字符串`123`时，则返回 值为 1，否则返回 0。

#  3. SQL 函数

##  3.1 字符串函数

![image-20211029171852893](https://gitee.com/AD-Gai-Code/pic-go/raw/master/202110291718038.png)

##  3.2 数值函数

![image-20211029172147159](https://gitee.com/AD-Gai-Code/pic-go/raw/master/202110291721170.png)

##  3.3 日期和时间函数

![image-20211029172220314](https://gitee.com/AD-Gai-Code/pic-go/raw/master/202110291722306.png)

##  3.4 流程函数

![image-20211029172345561](https://gitee.com/AD-Gai-Code/pic-go/raw/master/202110291723577.png)

##  3.5 其他常用函数

![image-20211029172507319](https://gitee.com/AD-Gai-Code/pic-go/raw/master/202110291725201.png)

