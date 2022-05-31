# 

### 1.创建表

语法格式：

```mysql
create table 表名(
	字段名1 数据类型,
	字段名2 数据类型,
	字段名3 数据类型,
	...
);
```

**关于MySQL当中字段的数据类型？以下只说常见的：**

```mysql
int
bigint	//长整型
float
double
char(长度)	//定长字符串存储空间大小固定，适合作为主键或外键
varchar(长度)		//变长字符串，存储空间等于实际数据空间，最多255
date	//日期，年月日
dateTime	//日期，年月日 时分秒 毫秒
time	//日期，时分秒
BLOB	//Binary Large OBject（二进制大对象）存储图片、视频等流媒体信息
CLOB	//Character Large OBject（字符大对象）存储大文本，比如4个G的字符串
...
```

**char 和 varchar 怎么选择？**

- 定长时插入一个数据会直接分配给定的长度空间，插入的数据不够长度没事，但如果超过长度就会报错。
- 变长时插入一个数据分配的空间是插入数据的大小，比较智能，但超过长度也会报错。
- **一般在存（性别、生日等）固定长度数据时选取char，存（简介、姓名）等非固定长度的数据时选用varchar**。
- char 效率比 varchar 高，因为 varchar 在存数据时会进行 if 判断。

**BLOB和CLOB类型的使用？**

电影表：t_movie
| id (int) | name (varchar) | playtime (date/char) | poster (BLOB) | plot (CLOB) |
| :------: | :------------: | :------------------: | :-----------: | :---------: |
|    1     |     蜘蛛侠     |                      |               |             |
|    2     |                |                      |               |             |
|    2     |                |                      |               |             |

insert 不能插入BLOB和CLOB，需要使用语言的IO流	//小图片可以存放到数据库中，比较方便。

表名在数据库中一般以 t_ 或者 tbl_ 开始。

**创建一个学生表，包括：学号、姓名、班级编号、生日？**

```mysql
mysql> create table t_student(no bigint,name varchar(255),sex char(1),classno varchar(255),birth char(10));
Query OK, 0 rows affected (0.03 sec)

mysql> show tables;
+---------------------+
| Tables_in_workSpace |
+---------------------+
| DEPT                |
| EMP                 |
| SALGRADE            |
| t_student           |
+---------------------+
4 rows in set (0.00 sec)

mysql> desc t_student;
+---------+--------------+------+-----+---------+-------+
| Field   | Type         | Null | Key | Default | Extra |
+---------+--------------+------+-----+---------+-------+
| no      | bigint       | YES  |     | NULL    |       |
| name    | varchar(255) | YES  |     | NULL    |       |
| sex     | char(1)      | YES  |     | NULL    |       |
| classno | varchar(255) | YES  |     | NULL    |       |
| birth   | char(10)     | YES  |     | NULL    |       |
+---------+--------------+------+-----+---------+-------+
5 rows in set (0.00 sec)
```

### 2.insert插入数据

语法格式：

```mysql
insert into 表名(字段名1,字段名2,字段名3,...) values(值1,值2,值3,...);
```

要求：字段数量和值数量相同，且数据类型对应相同。

举例：向表中插入一条数据

```mysql
mysql> insert into t_student(no,name,sex,classno,birth) values(15,'邹夏冰','男','软件1903','2001-03-25');
Query OK, 1 row affected (0.01 sec)

mysql> select * from t_student;
+------+-----------+------+------------+------------+
| no   | name      | sex  | classno    | birth      |
+------+-----------+------+------------+------------+
|   15 | 邹夏冰    | 男   | 软件1903   | 2001-03-25 |
+------+-----------+------+------------+------------+
1 row in set (0.00 sec)
```

**注意：**

- 只要字段名和值能对应上就行，字段的前后顺序不影响最后插入的效果。
- 可以少插入字段和对应的值，会被默认为NULL，甚至不插入值，所有的值都会被默认为NULL。
- 但凡成功执行一条 insert 语句，表中就一定会多一行记录。

#### default 默认值

建表：

```mysql
drop table if exists t_student;
create table t_student(
	no bigint,
    name varchar(255),
    sex char(1) default 男,
    classno varcahr(255),
    birth char(10)
);
```

插入：

```mysql
mysql> insert into t_student(name) values('邹夏冰');
Query OK, 1 row affected (0.02 sec)

mysql> select * from t_student;
+------+-----------+------+---------+-------+
| no   | name      | sex  | classno | birth |
+------+-----------+------+---------+-------+
| NULL | 邹夏冰    | 男   | NULL    | NULL  |
+------+-----------+------+---------+-------+
1 row in set (0.00 sec)
```

可以看到性别没有被赋值，所以就被默认为'男'了。

#### 插入时缺省字段名

```mysql
mysql> insert into t_student values(04193074,'邹夏冰','男','软件1903','2001-03-25');
Query OK, 1 row affected (0.05 sec)

mysql> select * from t_student;
+---------+-----------+------+------------+------------+
| no      | name      | sex  | classno    | birth      |
+---------+-----------+------+------------+------------+
|    NULL | 邹夏冰    | 男   | NULL       | NULL       |
|    NULL | NULL      | 男   | NULL       | NULL       |
| 4193074 | 邹夏冰    | 男   | 软件1903   | 2001-03-25 |
+---------+-----------+------+------------+------------+
3 rows in set (0.00 sec)
```

注意：缺省字段名时需要将后面的values中的值**按顺序全部赋值**。

**小细节**：若数字前方可能带有0，就必须用字符串类型，否则将无法显示0，因为计算机存储数字类型的值时是转换为二进制数存储的，会省略掉开头的0。

#### 一次插入多行数据

```mysql
mysql> insert into t_student values(1,'张三','男','软件1903','2001-01-01'),(2,' 四 ','女','软件1903','2001-02-02');
Query OK, 2 rows affected (0.01 sec)
Records: 2  Duplicates: 0  Warnings: 0

mysql> select * from t_student;
+---------+-----------+------+------------+------------+
| no      | name      | sex  | classno    | birth      |
+---------+-----------+------+------------+------------+
|    NULL | 邹夏冰    | 男   | NULL       | NULL       |
|    NULL | NULL      | 男   | NULL       | NULL       |
| 4193074 | 邹夏冰    | 男   | 软件1903   | 2001-03-25 |
|       1 | 张三      | 男   | 软件1903   | 2001-01-01 |
|       2 | 李四      | 女   | 软件1903   | 2001-02-02 |
+---------+-----------+------+------------+------------+
5 rows in set (0.00 sec)
```

用法： values(...),(...),...

- 同样的，字段名可以缺省全部，也可以缺省部分。

#### 表的复制

语法：

​	create table 表名 as SQL语句

```mysql
mysql> create table dept1 as select * from DEPT;
Query OK, 4 rows affected (0.01 sec)
Records: 4  Duplicates: 0  Warnings: 0

mysql> select * from dept1;
+--------+------------+----------+
| DEPTNO | DNAME      | LOC      |
+--------+------------+----------+
|     10 | ACCOUNTING | NEW YORK |
|     20 | RESEARCH   | DALLAS   |
|     30 | SALES      | CHICAGO  |
|     40 | OPERATIONS | BOSTON   |
+--------+------------+----------+
4 rows in set (0.00 sec)
```

会将 select 语句的最终结果作为一张表复制给创建的这张表。

#### 将查询结果插入到一张表中

语法：

​	insert  into  表名  SQL语句

```mysql
mysql> insert into dept1 select * from dept1;
Query OK, 4 rows affected (0.01 sec)
Records: 4  Duplicates: 0  Warnings: 0

mysql> select * from dept1;
+--------+------------+----------+
| DEPTNO | DNAME      | LOC      |
+--------+------------+----------+
|     10 | ACCOUNTING | NEW YORK |
|     20 | RESEARCH   | DALLAS   |
|     30 | SALES      | CHICAGO  |
|     40 | OPERATIONS | BOSTON   |
|     10 | ACCOUNTING | NEW YORK |
|     20 | RESEARCH   | DALLAS   |
|     30 | SALES      | CHICAGO  |
|     40 | OPERATIONS | BOSTON   |
+--------+------------+----------+
8 rows in set (0.00 sec)
```

注意：

- select 查出来的表必须与被插入的表的结构相同。

### 3.修改数据：update

语法格式：

​		update  表名  set 字段名1 = 值1，字段名2 = 值2...where  条件；

注意：

​		不添加条件会使这张表的整个字段的值都被更新。

**将部门10的LOC修改为SHANGHAI，将部门名称修改为PERSONNEL？**

```mysql
mysql> update dept1 set LOC = 'SHANGHAI',DNAME = 'PERSONNEL' where DEPTNO = 10;
Query OK, 0 rows affected (0.00 sec)
Rows matched: 2  Changed: 0  Warnings: 0

mysql> select * from dept1;
+--------+------------+----------+
| DEPTNO | DNAME      | LOC      |
+--------+------------+----------+
|     10 | PERSONNEL  | SHANGHAI |
|     20 | RESEARCH   | DALLAS   |
|     30 | SALES      | CHICAGO  |
|     40 | OPERATIONS | BOSTON   |
|     10 | PERSONNEL  | SHANGHAI |
|     20 | RESEARCH   | DALLAS   |
|     30 | SALES      | CHICAGO  |
|     40 | OPERATIONS | BOSTON   |
+--------+------------+----------+
8 rows in set (0.00 sec)
```

### 4.删除数据：delete

语法格式：

​	delete from 表名 where 条件；

**删除10部门数据？**

```mysql
mysql> delete from dept1 where DEPTNO = 10;
Query OK, 2 rows affected (0.01 sec)

mysql> select * from dept1;
+--------+------------+---------+
| DEPTNO | DNAME      | LOC     |
+--------+------------+---------+
|     20 | RESEARCH   | DALLAS  |
|     30 | SALES      | CHICAGO |
|     40 | OPERATIONS | BOSTON  |
|     20 | RESEARCH   | DALLAS  |
|     30 | SALES      | CHICAGO |
|     40 | OPERATIONS | BOSTON  |
+--------+------------+---------+
6 rows in set (0.00 sec)
```

**删除所有记录？**

```mysql
mysql> delete from dept1;
Query OK, 6 rows affected (0.01 sec)

mysql> select * from dept1;
Empty set (0.00 sec)
```

 **怎么删除大表？（重点）**

​	truncate table 表名;	//表被截断，不可回滚。永久丢失。

```mysql
mysql> select * from dept1;
+--------+------------+----------+
| DEPTNO | DNAME      | LOC      |
+--------+------------+----------+
|     10 | ACCOUNTING | NEW YORK |
|     20 | RESEARCH   | DALLAS   |
|     30 | SALES      | CHICAGO  |
|     40 | OPERATIONS | BOSTON   |
+--------+------------+----------+
4 rows in set (0.00 sec)

mysql> truncate table dept1;
Query OK, 0 rows affected (0.03 sec)

mysql> select * from dept1;
Empty set (0.00 sec)
```

**跟delete的区别？**

- delete 只删除数据，不摧毁表结构，相当于用橡皮擦掉了写的作业，truncate 相当于步步高打火机，哪里不会点哪里。
- 所以在处理大量完全不再需要的数据时使用 truncate 的删除速度会比 delete 快得多。

增删改查有一个术语： CRUD操作

Create（增）Retrieve（检索）Update（修改）Delete（删除）



