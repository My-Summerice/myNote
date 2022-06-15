# 查询语句（DQL）

### 1.简单的查询语句

#### 1.1 语法格式：

```mysql
select 字段名1，字段名2，字段名3，...from 表名;
```

注意：

- SQL语句必须以 ';' 结尾
- SQL语句不区分大小写

例：查询员工年薪？（字段可以参与数学运算）

```mysql
mysql> select ENAME,SAL * 12 from EMP;
+--------+----------+
| ENAME  | SAL * 12 |
+--------+----------+
| SMITH  |  9600.00 |
| ALLEN  | 19200.00 |
| WARD   | 15000.00 |
| JONES  | 35700.00 |
| MARTIN | 15000.00 |
| BLAKE  | 34200.00 |
| CLARK  | 29400.00 |
| SCOTT  | 36000.00 |
| KING   | 60000.00 |
| TURNER | 18000.00 |
| ADAMS  | 13200.00 |
| JAMES  | 11400.00 |
| FORD   | 36000.00 |
| MILLER | 15600.00 |
+--------+----------+
```

#### 1.2 使用as修改查询结果的列的名称：

```mysql
mysql> select ENAME,SAL * 12 as YEARSAL from EMP;
+--------+----------+
| ENAME  | YEARSAL  |
+--------+----------+
| SMITH  |  9600.00 |
| ALLEN  | 19200.00 |
| WARD   | 15000.00 |
| JONES  | 35700.00 |
| MARTIN | 15000.00 |
| BLAKE  | 34200.00 |
| CLARK  | 29400.00 |
| SCOTT  | 36000.00 |
| KING   | 60000.00 |
| TURNER | 18000.00 |
| ADAMS  | 13200.00 |
| JAMES  | 11400.00 |
| FORD   | 36000.00 |
| MILLER | 15600.00 |
+--------+----------+
```

重命名不能直接写汉字，使用单引号可以：

```mysql
mysql> select ENAME,SAL * 12 as '年薪' from EMP;
+--------+----------+
| ENAME  | 年薪     |
+--------+----------+
| SMITH  |  9600.00 |
| ALLEN  | 19200.00 |
| WARD   | 15000.00 |
| JONES  | 35700.00 |
| MARTIN | 15000.00 |
| BLAKE  | 34200.00 |
| CLARK  | 29400.00 |
| SCOTT  | 36000.00 |
| KING   | 60000.00 |
| TURNER | 18000.00 |
| ADAMS  | 13200.00 |
| JAMES  | 11400.00 |
| FORD   | 36000.00 |
| MILLER | 15600.00 |
+--------+----------+
```

注意：

- as关键字可省略
- 标准SQL要求字符串加单引号。mysql支持双引号但不建议使用。

### 2.条件查询

#### 2.1  语法格式：

```mysql
select
	字段，字段...
from 
	表名
where
	条件;
```

执行顺序：

​		from -> where -> select

例：

**查询工资为5000的员工姓名？**

```mysql
mysql> select ENAME from EMP where SAL = 5000;
+-------+
| ENAME |
+-------+
| KING  |
+-------+
```

**查询SMITH的工资？**

```mysql
mysql> select SAL from EMP where ENAME = 'SMITH';
+--------+
| SAL    |
+--------+
| 800.00 |
+--------+
```

**找出工资不低于3000的员工？**

```mysql
mysql> select ENAME from EMP where SAL >= 3000;
+-------+
| ENAME |
+-------+
| SCOTT |
| KING  |
| FORD  |
+-------+
```

**找出工资不等于3000的员工？**

```mysql
mysql> select ENAME from EMP where SAL <> 3000;
+--------+
| ENAME  |
+--------+
| SMITH  |
| ALLEN  |
| WARD   |
| JONES  |
| MARTIN |
| BLAKE  |
| CLARK  |
| KING   |
| TURNER |
| ADAMS  |
| JAMES  |
| MILLER |
+--------+
```

注意：<> 是不等于，!= 也可以使用

**找出工资在[1100,3000]之间的员工?**

```mysql
mysql> select ENAME,SAL from EMP where SAL >= 1100 and SAL <= 3000;
+--------+---------+
| ENAME  | SAL     |
+--------+---------+
| ALLEN  | 1600.00 |
| WARD   | 1250.00 |
| JONES  | 2975.00 |
| MARTIN | 1250.00 |
| BLAKE  | 2850.00 |
| CLARK  | 2450.00 |
| SCOTT  | 3000.00 |
| TURNER | 1500.00 |
| ADAMS  | 1100.00 |
| FORD   | 3000.00 |
| MILLER | 1300.00 |
+--------+---------+
```

使用between ... and ... 完成

```mysql
select ENAME,SAL form EMP where SAL between 1100 and 3000;
```

注意：

- between ... and ... 只适用于闭区间

- 翻译为 >= and <=

- 所以and周围的数据必须是左小右大

- 可以用在字符方面：select ENAME from EMP between 'A' and 'C';

  左闭右开，查不到‘C’开头的

**找出哪些人的津贴为NULL？**

```mysql
mysql> select ENAME,SAL,COMM from EMP where COMM is NULL;
+--------+---------+------+
| ENAME  | SAL     | COMM |
+--------+---------+------+
| SMITH  |  800.00 | NULL |
| JONES  | 2975.00 | NULL |
| BLAKE  | 2850.00 | NULL |
| CLARK  | 2450.00 | NULL |
| SCOTT  | 3000.00 | NULL |
| KING   | 5000.00 | NULL |
| ADAMS  | 1100.00 | NULL |
| JAMES  |  950.00 | NULL |
| FORD   | 3000.00 | NULL |
| MILLER | 1300.00 | NULL |
+--------+---------+------+
```

注意：

- NULL不是值，不能使用 = 判断
- 使用 is NULL 或者 is not NULL
- 判断条件 is null、is not null 属于SQL自带的字符，所以不区分大小写

**找出哪些人没有津贴？**

```mysql
mysql> select ENAME,SAL,COMM from EMP where COMM is NULL or COMM = 0;
+--------+---------+------+
| ENAME  | SAL     | COMM |
+--------+---------+------+
| SMITH  |  800.00 | NULL |
| JONES  | 2975.00 | NULL |
| BLAKE  | 2850.00 | NULL |
| CLARK  | 2450.00 | NULL |
| SCOTT  | 3000.00 | NULL |
| KING   | 5000.00 | NULL |
| TURNER | 1500.00 | 0.00 |
| ADAMS  | 1100.00 | NULL |
| JAMES  |  950.00 | NULL |
| FORD   | 3000.00 | NULL |
| MILLER | 1300.00 | NULL |
+--------+---------+------+
```

**找出工作岗位是MANAGER和SALESMAN的员工** ？

```mysql
mysql> select ENAME,JOB from EMP where JOB = 'MANAGER' or JOB = 'SALESMAN';
+--------+----------+
| ENAME  | JOB      |
+--------+----------+
| ALLEN  | SALESMAN |
| WARD   | SALESMAN |
| JONES  | MANAGER  |
| MARTIN | SALESMAN |
| BLAKE  | MANAGER  |
| CLARK  | MANAGER  |
| TURNER | SALESMAN |
+--------+----------+
```

**找出薪资大于1000并且部门编号是20或30的员工**

```mysql
mysql> select ENAME,SAL,DEPTNO from EMP  where SAL > 1000 and DEPTNO = 20 or DEPTNO = 30;
+--------+---------+--------+
| ENAME  | SAL     | DEPTNO |
+--------+---------+--------+
| ALLEN  | 1600.00 |     30 |
| WARD   | 1250.00 |     30 |
| JONES  | 2975.00 |     20 |
| MARTIN | 1250.00 |     30 |
| BLAKE  | 2850.00 |     30 |
| SCOTT  | 3000.00 |     20 |
| TURNER | 1500.00 |     30 |
| ADAMS  | 1100.00 |     20 |
| JAMES  |  950.00 |     30 |	//看下方注意
| FORD   | 3000.00 |     20 |
+--------+---------+--------+
```

注意：

- and优先级比or高，**所以上方查询结果不准确**
- SQL语句可以添加小括号来给予优先级：

```mysql
mysql> select ENAME,SAL,DEPTNO from EMP  where SAL > 1000 and (DEPTNO = 20 or DEPTNO = 30);
+--------+---------+--------+
| ENAME  | SAL     | DEPTNO |
+--------+---------+--------+
| ALLEN  | 1600.00 |     30 |
| WARD   | 1250.00 |     30 |
| JONES  | 2975.00 |     20 |
| MARTIN | 1250.00 |     30 |
| BLAKE  | 2850.00 |     30 |
| SCOTT  | 3000.00 |     20 |
| TURNER | 1500.00 |     30 |
| ADAMS  | 1100.00 |     20 |
| FORD   | 3000.00 |     20 |
+--------+---------+--------+
```

- in 等同于 or：

  `select ENAME,JOB from EMP where JOB = 'MANAGER' or JOB = 'SALESMAN';`等同于

  `select ENAME,JOB from EMP where JOB in ('SALESMAN','MANAGER');`

  - in 括号中的不是区间，是具体的值
  - not in

### 3.模糊查询like

在模糊查询中必须掌握两个特殊符号，% 和 _

- % 代表任意多个字符
- _  代表任意一个字符

**找出名字含O的员工？**

```mysql
mysql> select ENAME from EMP where ENAME like '%O%';
+-------+
| ENAME |
+-------+
| JONES |
| SCOTT |
| FORD  |
+-------+
```

**找出第二个字母是A的员工？**

```mysql
mysql> select ENAME from EMP where ENAME like '_A%';
+--------+
| ENAME  |
+--------+
| WARD   |
| MARTIN |
| JAMES  |
+--------+
```

**找出名字中有_的员工？**

```mysql
select ENAME from EMP where ENAME like '%\_%';
```

### 4.排序（升序、降序）

**按照工资升序，找出员工名和薪资？**

```mysql
mysql> select ENAME,SAL from EMP order by SAL;
+--------+---------+
| ENAME  | SAL     |
+--------+---------+
| SMITH  |  800.00 |
| JAMES  |  950.00 |
| ADAMS  | 1100.00 |
| WARD   | 1250.00 |
| MARTIN | 1250.00 |
| MILLER | 1300.00 |
| TURNER | 1500.00 |
| ALLEN  | 1600.00 |
| CLARK  | 2450.00 |
| BLAKE  | 2850.00 |
| JONES  | 2975.00 |
| SCOTT  | 3000.00 |
| FORD   | 3000.00 |
| KING   | 5000.00 |
+--------+---------+
```

- 默认是升序

升序 asc：

```mysql
mysql> select ENAME,SAL from EMP order by SAL asc;
+--------+---------+
| ENAME  | SAL     |
+--------+---------+
| SMITH  |  800.00 |
| JAMES  |  950.00 |
| ADAMS  | 1100.00 |
| WARD   | 1250.00 |
| MARTIN | 1250.00 |
| MILLER | 1300.00 |
| TURNER | 1500.00 |
| ALLEN  | 1600.00 |
| CLARK  | 2450.00 |
| BLAKE  | 2850.00 |
| JONES  | 2975.00 |
| SCOTT  | 3000.00 |
| FORD   | 3000.00 |
| KING   | 5000.00 |
+--------+---------+
```

降序 desc：

```mysql
mysql> select ENAME,SAL from EMP order by SAL desc;
+--------+---------+
| ENAME  | SAL     |
+--------+---------+
| KING   | 5000.00 |
| SCOTT  | 3000.00 |
| FORD   | 3000.00 |
| JONES  | 2975.00 |
| BLAKE  | 2850.00 |
| CLARK  | 2450.00 |
| ALLEN  | 1600.00 |
| TURNER | 1500.00 |
| MILLER | 1300.00 |
| WARD   | 1250.00 |
| MARTIN | 1250.00 |
| ADAMS  | 1100.00 |
| JAMES  |  950.00 |
| SMITH  |  800.00 |
+--------+---------+
```

**按照工资降序排列，当工资相同时再按名字升序排？**

```mysql
mysql> select ENAME,SAL from EMP order by SAL desc , ENAME asc;
+--------+---------+
| ENAME  | SAL     |
+--------+---------+
| KING   | 5000.00 |
| FORD   | 3000.00 |
| SCOTT  | 3000.00 |
| JONES  | 2975.00 |
| BLAKE  | 2850.00 |
| CLARK  | 2450.00 |
| ALLEN  | 1600.00 |
| TURNER | 1500.00 |
| MILLER | 1300.00 |
| MARTIN | 1250.00 |
| WARD   | 1250.00 |
| ADAMS  | 1100.00 |
| JAMES  |  950.00 |
| SMITH  |  800.00 |
+--------+---------+
```

注意：

​		order by 后越靠前的字段排序优先级越高，只有该字段排序出现相等的情况时，才会将这些相等的数据再按照下一个字段的排序方式排序，以此类推。

- order by 后接数字代表按第几列的字段排序，但不建议

**找出工作岗位是SALESMAN的员工，并且按照工资降序排序？**

```mysql
mysql> select ENAME,SAL from EMP  where JOB = 'SALESMAN' order by SAL desc;
+--------+---------+
| ENAME  | SAL     |
+--------+---------+
| ALLEN  | 1600.00 |
| TURNER | 1500.00 |
| WARD   | 1250.00 |
| MARTIN | 1250.00 |
+--------+---------+
```

执行顺序：

​		from -> where -> select -> order by

### 5.分组函数

count	计数

sum	求和

avg	平均值

max	最大值

min	最小值

**找出工资总和？**

```mysql
mysql> select sum(SAL) from EMP;
+----------+
| sum(SAL) |
+----------+
| 29025.00 |
+----------+
```

**找出最高工资？**

```mysql
mysql> select max(SAL) from EMP;
+----------+
| max(SAL) |
+----------+
|  5000.00 |
+----------+
```

**找出最低工资？**

```mysql
mysql> select min(SAL) from EMP;
+----------+
| min(SAL) |
+----------+
|   800.00 |
+----------+
```

**找出平均工资？**

```mysql
mysql> select avg(SAL) from EMP;
+-------------+
| avg(SAL)    |
+-------------+
| 2073.214286 |
+-------------+
```

**找出总人数？**

```mysql
mysql> select count(ENAME) from EMP;	//count(*)也可找出总人数
+--------------+
| count(ENAME) |
+--------------+
|           14 |
+--------------+
```

注意：

- count(*)：不是统计某个字段中的数据条数，而是统计总记录条数。（和某个字段无关）

  count(字段名)：统计该字段中不为NULL的数据的总个数。

- 又名多行处理函数。特点：输入多行，输出一行。

- **分组函数会自动忽略NULL**，比如： 

  select count(COMM) from EMP;

  COMM列下有很多NULL，在计算时会被忽略。

- 分组函数可以连着使用，比如：

  `select sum(SAL),count(SAL),max(SAL),min(SAL),avg(SAL) from EMP;`

**找出工资高于平均工资的员工？**

```mysql
mysql> select ENAME,SAL from EMP where SAL > avg(SAL);
ERROR 1111 (HY000): Invalid use of group function
```

原因： SQL语句语法规定，分组函数不可使用在where子句中。

**why？？？**

**因为** group by 是在 where 之后执行的：

(5)select 

​			...

 (1)from 

​			... 

(2)where 

​			...

 (3)group by

​			...

 (4)having 

​			...

 (6)order by 

​			...;

执行顺序：1 2 3 4 5 6

而分组函数必须在**分组**之后才能使用，所以where子句中不能使用分组函数，即使没有group by 所有数据也会自成一组。

改正上方答案：（子查询）

```mysql
mysql> select ENAME,SAL from EMP where SAL > (select avg(SAL) from EMP);
+-------+---------+
| ENAME | SAL     |
+-------+---------+
| JONES | 2975.00 |
| BLAKE | 2850.00 |
| CLARK | 2450.00 |
| SCOTT | 3000.00 |
| KING  | 5000.00 |
| FORD  | 3000.00 |
+-------+---------+
```



### 6.单行处理函数

#### 6.1 什么是单行处理函数？

​		输入一行，输出一行。

**计算每个员工的年薪？**

```mysql
mysql> select ENAME,(SAL + COMM) * 12 as YEARSAL from EMP;
+--------+----------+
| ENAME  | YEARSAL  |
+--------+----------+
| SMITH  |     NULL |
| ALLEN  | 22800.00 |
| WARD   | 21000.00 |
| JONES  |     NULL |
| MARTIN | 31800.00 |
| BLAKE  |     NULL |
| CLARK  |     NULL |
| SCOTT  |     NULL |
| KING   |     NULL |
| TURNER | 18000.00 |
| ADAMS  |     NULL |
| JAMES  |     NULL |
| FORD   |     NULL |
| MILLER |     NULL |
+--------+----------+
```

注意：

- 在数据库中，只要算术式中含有NULL，整个式子的结果都是NULL，所以上方查询不正确

#### 6.2 ifnull()空处理函数

语法格式：

​		ifnull(可能为NULL的数据, 被当作什么处理)：**属于单行处理函数（一行一行执行）**。

**改正上方计算员工年薪的SQL语句？**

```mysql
mysql> select ENAME,(SAL+ifnull(COMM,0))*12 as YEARSAL from EMP;
+--------+----------+
| ENAME  | YEARSAL  |
+--------+----------+
| SMITH  |  9600.00 |
| ALLEN  | 22800.00 |
| WARD   | 21000.00 |
| JONES  | 35700.00 |
| MARTIN | 31800.00 |
| BLAKE  | 34200.00 |
| CLARK  | 29400.00 |
| SCOTT  | 36000.00 |
| KING   | 60000.00 |
| TURNER | 18000.00 |
| ADAMS  | 13200.00 |
| JAMES  | 11400.00 |
| FORD   | 36000.00 |
| MILLER | 15600.00 |
+--------+----------+
```

### 7.group by 和 having

group by： 按照某个字段或者某些字段进行分组。

having ： having是对分组之后的数据进行再次过滤。

语法格式：

​		select max(SAL) from EMP group by JOB;

注意：

- 分组函数一般都会与 group by 结合使用，这也是为什么被称为分组函数的原因。
- 任何一个分组函数（count sum avg max min）都是在 group by 之后才会执行。
- 任何一个SQL语句没有 group by 的话，整张表的数据会自成一组

**找出每个工作岗位的最高薪资？**

```mysql
mysql> select JOB,max(SAL) from EMP group by JOB;
+-----------+----------+
| JOB       | max(SAL) |
+-----------+----------+
| CLERK     |  1300.00 |
| SALESMAN  |  1600.00 |
| MANAGER   |  2975.00 |
| ANALYST   |  3000.00 |
| PRESIDENT |  5000.00 |
+-----------+----------+
```

注意：

- 当一个SQL语句中出现 group by 时，select 后只能跟分组函数和参与分组的字段。如果跟了无关的字段是无意义的，MySQL不会报错但Oracel数据库中会报错，语法错误，因为Oracel的语法规则更加严谨。

**找出每个部门不同工作岗位的最高薪资？**

```mysql
mysql> select DEPTNO,JOB,max(SAL) from EMP group by DEPTNO,JOB order by DEPTNO;
+--------+-----------+----------+
| DEPTNO | JOB       | max(SAL) |
+--------+-----------+----------+
|     10 | CLERK     |  1300.00 |
|     10 | MANAGER   |  2450.00 |
|     10 | PRESIDENT |  5000.00 |
|     20 | ANALYST   |  3000.00 |
|     20 | CLERK     |  1100.00 |
|     20 | MANAGER   |  2975.00 |
|     30 | CLERK     |   950.00 |
|     30 | MANAGER   |  2850.00 |
|     30 | SALESMAN  |  1600.00 |
+--------+-----------+----------+
```

**找出每个部门的最高薪资，要求显示薪资大于2900的数据？**

```mysql
mysql> select DEPTNO,max(SAL) from EMP where SAL > 2900 group by DEPTNO;
+--------+----------+
| DEPTNO | max(SAL) |
+--------+----------+
|     20 |  3000.00 |
|     10 |  5000.00 |
+--------+----------+
```

注意：

- 能使用 where 就使用 where 因为它会优先过滤掉一些信息再进行分组，这样会使效率提高。

**找出每个部门的平均薪资，要求显示薪资大于2000的数据？**

```mysql
mysql> select DEPTNO,avg(SAL) from EMP group by DEPTNO having avg(SAL) > 2000;
+--------+-------------+
| DEPTNO | avg(SAL)    |
+--------+-------------+
|     20 | 2175.000000 |
|     10 | 2916.666667 |
+--------+-------------+
```

注意：

- 因为where子句中不能使用分组函数，所以，当比较条件中的数据是必须要经过分组函数计算时就不能使用 where 先过滤数据再分组，而是应该先分组再使用 having 过滤数据。

### 8. 关于查询结果集的去重

```mysql
mysql> select distinct JOB from EMP;	//distinct关键字去重
+-----------+
| JOB       |
+-----------+
| CLERK     |
| SALESMAN  |
| MANAGER   |
| ANALYST   |
| PRESIDENT |
+-----------+
```

注意：

- distinct 只能出现在所有字段前方

- distinct 后方所有字段联合去重

  **为什么？？？**：因为经过 distinct 对字段去重后，有可能会出现数据条数变少的情况，这时若有多个字段，那么数据条数将会不匹配，无法形成一个完整的表格。所以SQL语法不允许 distinct 前方出现字段且 distinct 会对它后方的所有字段进行联合去重。

**统计岗位数？**

```mysql
mysql> select count(distinct JOB) from EMP;
+---------------------+
| count(distinct JOB) |
+---------------------+
|                   5 |
+---------------------+
```

### 9.连接查询

#### 9.1 分类：

根据语法年代划分：

- SQL92（一些老DBA可能还在使用，DataBase Administrator,数据库管理员）
- SQL99（较新的语法）

根据表的连接方式划分：

- 内连接：
  - 等值连接
  - 非等值连接
  - 自连接
- 外连接：
  - 左外连接（左连接）
  - 右外连接（右连接）
- 全连接（很少用！）

#### 9.2 笛卡尔积现象

##### 9.2.1 笛卡尔积现象：如果对两张表进行连接查询时没有任何条件限制，那么最终查出来的表的条数是这两张表的条数的乘积。

**找出每一个员工的部门名称，要求显示员工名和部门名？**

```mysql
mysql> select ENAME,DNAME from EMP,DEPT;
+--------+------------+
| ENAME  | DNAME      |
+--------+------------+
| SMITH  | OPERATIONS |
| SMITH  | SALES      |
| SMITH  | RESEARCH   |
| SMITH  | ACCOUNTING |
| ALLEN  | OPERATIONS |
| ALLEN  | SALES      |
.........
56 rows in set (0.01 sec)
```

**关于表的别名**：

```mysql
select e.ENAME,d.DNAME from EMP e,DEPT d;
```

表的别名有什么好处?

- 执行效率高
- 可读性好

##### 9.2.2 如何避免笛卡尔积现象？

**注意**：加条件过滤可避免笛卡尔积现象，但是**并不会减少记录的匹配次数**，不会提升效率，**只是将有效的记录显示出来**。

所以，改进上方SQL语句：

```mysql
mysql> select e.ENAME,d.DNAME from EMP e,DEPT d where e.DEPTNO = d.DEPTNO;	//SQL92，老语法，以后不用
+--------+------------+
| ENAME  | DNAME      |
+--------+------------+
| SMITH  | RESEARCH   |
| ALLEN  | SALES      |
| WARD   | SALES      |
| JONES  | RESEARCH   |
| MARTIN | SALES      |
| BLAKE  | SALES      |
| CLARK  | ACCOUNTING |
| SCOTT  | RESEARCH   |
| KING   | ACCOUNTING |
| TURNER | SALES      |
| ADAMS  | RESEARCH   |
| JAMES  | SALES      |
| FORD   | RESEARCH   |
| MILLER | ACCOUNTING |
+--------+------------+
```

#### 9.3 内连接

##### 9.3.1 等值连接

特点：连接条件中的关系是等量关系。

**查询每个员工的部门名称，要求显示员工和部门名？**

SQL92：（太老了，不用了）

```mysql
select e.ENAME,d.DNAME from EMP e,DEPT d where e.DEPTNO = d.DEPTNO;
```

SQL99：（常用的） 

```mysql
mysql> select e.ENAME,d.DNAME from EMP e inner join DEPT d on e.DEPTNO = d.DEPTNO;
+--------+------------+
| ENAME  | DNAME      |
+--------+------------+
| SMITH  | RESEARCH   |
| ALLEN  | SALES      |
| WARD   | SALES      |
| JONES  | RESEARCH   |
| MARTIN | SALES      |
| BLAKE  | SALES      |
| CLARK  | ACCOUNTING |
| SCOTT  | RESEARCH   |
| KING   | ACCOUNTING |
| TURNER | SALES      |
| ADAMS  | RESEARCH   |
| JAMES  | SALES      |
| FORD   | RESEARCH   |
| MILLER | ACCOUNTING |
+--------+------------+
```

注意：

- 将SQL92 from中的 ，替换成  inner join 再把 where 替换为 on。
- inner join 中的 inner 可以省略，但是带上可读性很好。
- on 之后还能添加 where 进行过滤。
- 好处：将表连接的条件从where中分离出来，更加清晰。

##### 9.3.2 非等值连接

特点：连接条件中的关系是非等量关系。

**找出每个员工的工资等级，要求显示员工名、工资、工资等级？**

```mysql
mysql> select e.ENAME,e.SAL,s.GRADE from EMP e inner join SALGRADE s on e.SAL between s.LOSAL and s.HISAL order by e.SAL;
+--------+---------+-------+
| ENAME  | SAL     | GRADE |
+--------+---------+-------+
| SMITH  |  800.00 |     1 |
| JAMES  |  950.00 |     1 |
| ADAMS  | 1100.00 |     1 |
| WARD   | 1250.00 |     2 |
| MARTIN | 1250.00 |     2 |
| MILLER | 1300.00 |     2 |
| TURNER | 1500.00 |     3 |
| ALLEN  | 1600.00 |     3 |
| CLARK  | 2450.00 |     4 |
| BLAKE  | 2850.00 |     4 |
| JONES  | 2975.00 |     4 |
| SCOTT  | 3000.00 |     4 |
| FORD   | 3000.00 |     4 |
| KING   | 5000.00 |     5 |
+--------+---------+-------+
```

#####  9.3.3 自连接

特点：一张表看作两张表，自己连接自己。

**找出每个员工的上级领导，要求显示员工名和对应的领导名？**

```mysql
mysql> select e.ENAME,l.ENAME as leader from EMP e inner join EMP l on e.MGR = l.EMPNO order by leader;
+--------+--------+
| ENAME  | leader |
+--------+--------+
| ALLEN  | BLAKE  |
| WARD   | BLAKE  |
| MARTIN | BLAKE  |
| TURNER | BLAKE  |
| JAMES  | BLAKE  |
| MILLER | CLARK  |
| SMITH  | FORD   |
| SCOTT  | JONES  |
| FORD   | JONES  |
| JONES  | KING   |
| BLAKE  | KING   |
| CLARK  | KING   |
| ADAMS  | SCOTT  |
+--------+--------+
```

注意：

- 自连接中可以使用等值连接和非等值连接

#### 9.4 外连接

##### 9.4.1 什么是外连接，和内连接的区别？

- 内连接：A表和B表如果有能匹配上的记录就查出来。两张表没有主次之分。
- 外连接：A表和B表一张是主表，一张是副表，主要查询主表，顺带查询副表，如果副表中没有与主表相匹配的记录，就自动模拟出NULL与之匹配。

##### 9.4.2 外连接分类

- 左外连接（左连接）：表示左边的这张表是主表。
- 右外连接（右链接）：表示右边的这张表是主表。
- 左连接有有右连接的写法，右连接也会有对应左连接的写法。

**查找每个员工的上级领导，要求显示员工名和其对应领导名？**（必须查出所有员工）

```mysql
mysql> select a.ENAME,b.ENAME leader from EMP a left outer join EMP b on a.MGR = b.EMPNO;	//左外连接
+--------+--------+
| ENAME  | leader |
+--------+--------+
| SMITH  | FORD   |
| ALLEN  | BLAKE  |
| WARD   | BLAKE  |
| JONES  | KING   |
| MARTIN | BLAKE  |
| BLAKE  | KING   |
| CLARK  | KING   |
| SCOTT  | JONES  |
| KING   | NULL   |
| TURNER | BLAKE  |
| ADAMS  | SCOTT  |
| JAMES  | BLAKE  |
| FORD   | JONES  |
| MILLER | CLARK  |
+--------+--------+
```

注意：

- outer 可省略。
- 对应的右外连接SQL写法：

```mysql
select a.ENAME,b.ENAME leader from EMP b right join EMP a on a.MGR = b.EMPNO;
```

- 右外连接同理。

**找出哪个部门没有员工？**

```mysql
mysql> select d.* from DEPT d left outer join EMP e on d.DEPTNO = e.DEPTNO where e.DEPTNO is NULL;
+--------+------------+--------+
| DEPTNO | DNAME      | LOC    |
+--------+------------+--------+
|     40 | OPERATIONS | BOSTON |
+--------+------------+--------+	//左连接
```

#### 9.5 三表连查

**找出每一个员工的部门名称以及工资等级？**

```mysql
mysql> select e.ENAME,d.DNAME,s.GRADE from EMP e join DEPT d join SALGRADE s on e.DEPTNO = d.DEPTNO and e.SAL between s.LOSAL and s.HISAL;
+--------+------------+-------+
| ENAME  | DNAME      | GRADE |
+--------+------------+-------+
| SMITH  | RESEARCH   |     1 |
| ALLEN  | SALES      |     3 |
| WARD   | SALES      |     2 |
| JONES  | RESEARCH   |     4 |
| MARTIN | SALES      |     2 |
| BLAKE  | SALES      |     4 |
| CLARK  | ACCOUNTING |     4 |
| SCOTT  | RESEARCH   |     4 |
| KING   | ACCOUNTING |     5 |
| TURNER | SALES      |     3 |
| ADAMS  | RESEARCH   |     1 |
| JAMES  | SALES      |     1 |
| FORD   | RESEARCH   |     4 |
| MILLER | ACCOUNTING |     2 |
+--------+------------+-------+
```

**找出每一个员工的部门名称、薪资等级以及上级领导？**

```mysql
mysql> select a.ENAME,d.DNAME,s.GRADE,b.ENAME leader from EMP a join DEPT d join SALGRADE s on
a.DEPTNO = d.DEPTNO and a.SAL between s.LOSAL and s.HISAL left join EMP b on a.MGR = b.EMPNO;
+--------+------------+-------+--------+
| ENAME  | DNAME      | GRADE | leader |
+--------+------------+-------+--------+
| SMITH  | RESEARCH   |     1 | FORD   |
| ALLEN  | SALES      |     3 | BLAKE  |
| WARD   | SALES      |     2 | BLAKE  |
| JONES  | RESEARCH   |     4 | KING   |
| MARTIN | SALES      |     2 | BLAKE  |
| BLAKE  | SALES      |     4 | KING   |
| CLARK  | ACCOUNTING |     4 | KING   |
| SCOTT  | RESEARCH   |     4 | JONES  |
| KING   | ACCOUNTING |     5 | NULL   |
| TURNER | SALES      |     3 | BLAKE  |
| ADAMS  | RESEARCH   |     1 | SCOTT  |
| JAMES  | SALES      |     1 | BLAKE  |
| FORD   | RESEARCH   |     4 | JONES  |
| MILLER | ACCOUNTING |     2 | CLARK  |
+--------+------------+-------+--------+
```

**找出在哪个城市工作的员工最多，并显示他们的名称、部门、工资等级以及城市？**

```mysql
查询出现次数最多的记录，需要使用limit，往后看！！！
```

### 10.子查询

#### 10.1 什么是子查询？可以用在哪？

select语句当中嵌套select语句，被嵌套的select语句是子查询。

出现在哪里？

​	select

​		..(select).

​	from

​		..(select).

​	where

​		..(select).

#### 10.2 where子句中使用子查询

**找出高于平均薪资的员工信息？**

```mysql
mysql> select * from EMP where SAL > (select avg(SAL) from EMP);
+-------+-------+-----------+------+------------+---------+------+--------+
| EMPNO | ENAME | JOB       | MGR  | HIREDATE   | SAL     | COMM | DEPTNO |
+-------+-------+-----------+------+------------+---------+------+--------+
|  7566 | JONES | MANAGER   | 7839 | 1981-04-02 | 2975.00 | NULL |     20 |
|  7698 | BLAKE | MANAGER   | 7839 | 1981-05-01 | 2850.00 | NULL |     30 |
|  7782 | CLARK | MANAGER   | 7839 | 1981-06-09 | 2450.00 | NULL |     10 |
|  7788 | SCOTT | ANALYST   | 7566 | 1987-04-19 | 3000.00 | NULL |     20 |
|  7839 | KING  | PRESIDENT | NULL | 1981-11-17 | 5000.00 | NULL |     10 |
|  7902 | FORD  | ANALYST   | 7566 | 1981-12-03 | 3000.00 | NULL |     20 |
+-------+-------+-----------+------+------------+---------+------+--------+
6 rows in set (0.00 sec)
```

#### 10.3 from后面嵌套子查询

**找出每个部门平均薪资的薪资等级？**

```mysql
select d.DNAME,t.avgsal,t.GRADE from DEPT d join (select a.*,s.GRADE from SALGRADE s join (select DEPTNO,avg(SAL) avgsal from EMP group by DEPTNO) a on a.avgsal between s.LOSAL and s.HISAL) t on t.DEPTNO = d.DEPTNO;
```

**步骤：**

第一步：找出每个部门平均薪资（按部门编号分组，求SAL的平均值）

```mysql
mysql> select DEPTNO,avg(SAL) from EMP group by DEPTNO;
+--------+-------------+
| DEPTNO | avg(SAL)    |
+--------+-------------+
|     20 | 2175.000000 |
|     30 | 1566.666667 |
|     10 | 2916.666667 |
+--------+-------------+
3 rows in set (0.00 sec)
```

第二步：将以上查询结果当作临时表 a ，让 a 表和 SALGRADE s 表连接，查出对应的薪资等级

```mysql
mysql> select a.*,s.GRADE from SALGRADE s join (select DEPTNO,avg(SAL) avgsal from EMP group by DEPTNO
) a on a.avgsal between s.LOSAL and s.HISAL;
+--------+-------------+-------+
| DEPTNO | avgsal      | GRADE |
+--------+-------------+-------+
|     20 | 2175.000000 |     4 |
|     30 | 1566.666667 |     3 |
|     10 | 2916.666667 |     4 |
+--------+-------------+-------+
3 rows in set (0.05 sec)
```

**注意**：必须给子查询中含有分组函数的字段重命名，否则在子查询外调用会报错：`ERROR 1630 (42000): FUNCTION a.avg does not exist. Check the 'Function Name Parsing and Resolution' section in the Reference Manual`

第三步：将以上查询结果当作临时表 t ，让 t 表和 DEPT d 表连接，查出对应的部门名称

```mysql
mysql> select d.DNAME,t.avgsal,t.GRADE from DEPT d join (select a.*,s.GRADE from SALGRADE s join (select DEPTNO,avg(SAL) avgsal from EMP group by DEPTNO) a on a.avgsal between s.LOSAL and s.HISAL) t on t.DEPTNO = d.DEPTNO;
+------------+-------------+-------+
| DNAME      | avgsal      | GRADE |
+------------+-------------+-------+
| SALES      | 1566.666667 |     3 |
| RESEARCH   | 2175.000000 |     4 |
| ACCOUNTING | 2916.666667 |     4 |
+------------+-------------+-------+
3 rows in set (0.00 sec)
```

**找出每个部门薪资等级的平均值？**

```mysql
mysql> select d.DNAME,m.avggrade from DEPT d join (select t.DEPTNO,avg(t.GRADE) avggrade from
(select e.DEPTNO,s.GRADE from EMP e join SALGRADE s on SAL between s.LOSAL and s.HISAL) t grou
p by t.DEPTNO) m on d.DEPTNO = m.DEPTNO;
+------------+----------+
| DNAME      | avggrade |
+------------+----------+
| ACCOUNTING |   3.6667 |
| RESEARCH   |   2.8000 |
| SALES      |   2.5000 |
+------------+----------+
3 rows in set (0.01 sec)
```

**步骤：**

第一步：找出每个人的薪资等级，并显示出部门编号和薪资等级。

第二步：将第一步生成的临时表命名为 t ，使用 group by 将 t 表按部门编号分组并计算等级平均值，最后显示部门编号和平均等级（记得重命名，否则无法在外部调用）。

第三步：将第二步生成的临时表命名为 m ，将 t 表与 DEPT 表连接，查询并显示出部门名称及对应的薪资等级的平均值。

**注意**：

- 在写所有的SQL语句时都应该先把问题用文字描述出来，然后再将问题拆分成几个简单的部分最后再进行整合。切记不要上来一次写完，一定要分步骤，像上个问题一样的解决步骤就很好。

- 切记每个临时表都必须有自己的别名，不可直接使用。

#### 10.4 select后嵌套子查询

**找出每个员工所在的部门名称，要求显示员工名和部门名？**

```mysql
select e.ENAME,(select d.DNAME from DEPT where e.DEPTNO = d.DEPTNO) as DNAME from EMP e;
```

以上这种方法可以但不建议。只是为了练习此节内容。

建议使用：

```mysql
select e.ENAME,d.DNAME from EMP e join DEPT d on e.DEPTNO = d.DEPTNO;
```

### 11.union

作用：可以将查询结果集相加。

**找出岗位是SALESMAN和MANAGER的员工？**

第一种：`select ENAME,JOB from EMP where JOB = 'MANAGER' or JOB = 'SALESMAN';` 

第二种：`select ENAME,JOB from EMP where JOB in('MANAGER','SALESMAN');`

```mysql
+--------+----------+
| ENAME  | JOB      |
+--------+----------+
| ALLEN  | SALESMAN |
| WARD   | SALESMAN |
| JONES  | MANAGER  |
| MARTIN | SALESMAN |
| BLAKE  | MANAGER  |
| CLARK  | MANAGER  |
| TURNER | SALESMAN |
+--------+----------+
7 rows in set (0.00 sec)
```

第三种：	//使用union相加

```mysql
mysql> select ENAME,JOB from EMP where JOB = 'SALESMAN' union select ENAME,JOB from EMP where
JOB = 'MANAGER';
+--------+----------+
| ENAME  | JOB      |
+--------+----------+
| ALLEN  | SALESMAN |
| WARD   | SALESMAN |
| MARTIN | SALESMAN |
| TURNER | SALESMAN |
| JONES  | MANAGER  |
| BLAKE  | MANAGER  |
| CLARK  | MANAGER  |
+--------+----------+
7 rows in set (0.01 sec)
```

**注意：**

- 前两种查询结果是完全一样的。
- 使用 union 是先分别查询出结果再将结果合并成一张新的表，所以可以从结果看出第三种的 JOB 没有经过排序就是相同的 JOB 连在一块，而不同 JOB 的先后顺序取决于你写的 SQL 中的顺序，先写的 SALESMAN ，所以查询出来的 SALESMAN 会出现在前面。
- 最终查出来的表的字段是第一个 select 后面的字段，可以重命名。
- 使用 union 连接的句子查询出来的结果的列数必须相同，否则表将无法拼接，会报错。
- 

**！将两张不相干的表中的数据拼接在一起显示？**

```mysql
mysql> select ENAME from EMP union select DNAME from DEPT;
+------------+
| ENAME      |
+------------+
| SMITH      |
| ALLEN      |
| WARD       |
| JONES      |
| MARTIN     |
| BLAKE      |
| CLARK      |
| SCOTT      |
| KING       |
| TURNER     |
| ADAMS      |
| JAMES      |
| FORD       |
| MILLER     |
| ACCOUNTING |
| RESEARCH   |
| SALES      |
| OPERATIONS |
+------------+
18 rows in set (0.00 sec)
```

这个是 or 和 in 做不到的。

### 12.limit（重点中的重点，分页查询全靠它）

#### 12.1 limit是mysql特有的，其他数据库中没有，不通用。（Oracel中有一个相同的机制，叫作rownum)

#### 12.2 limit取结果集中的部分数据，这是它的作用。

#### 12.3 语法机制：

```mysql
limit startIndex,length
```

注意：

- startIndex表示起始位置。 //从0开始，0表示第一条数据
- length表示长度（取几个）

**取出工资前五名的员工？**

```mysql
mysql> select ENAME,SAL from EMP order by SAL desc limit 0,5;
+-------+---------+
| ENAME | SAL     |
+-------+---------+
| KING  | 5000.00 |
| FORD  | 3000.00 |
| SCOTT | 3000.00 |
| JONES | 2975.00 |
| BLAKE | 2850.00 |
+-------+---------+
5 rows in set (0.01 sec)
```

注意：

- sartIndex 省略时默认为0。
- limit 是SQL语句中最后执行的一个环节，在order by之后。

**找出工资排名在4到9名的员工？**

```mysql
mysql> select ENAME,SAL from EMP order by SAL desc limit 3,6;
+--------+---------+
| ENAME  | SAL     |
+--------+---------+
| JONES  | 2975.00 |
| BLAKE  | 2850.00 |
| CLARK  | 2450.00 |
| ALLEN  | 1600.00 |
| TURNER | 1500.00 |
| MILLER | 1300.00 |
+--------+---------+
6 rows in set (0.00 sec)
```

#### 12.4 通用的标准分页SQL

每页显示三条记录：

第一页： 0,3

第二页： 3,3

第三页： 6,3

...

每页显示pageSize条记录：

第pageNo页：（pageNo - 1)*pageSize,pageSize

