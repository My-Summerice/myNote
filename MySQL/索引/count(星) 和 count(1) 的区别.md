## count(*) 和 count(1) 的区别

[toc]

#### 1.性能优先级

`count(*) = count(1) > count(主键字段) > count(字段)`

count() 聚合函数是用来统计符合查询条件的记录中，该函数指定的参数不为 NULL 的记录条数。

#### 2.count(主键字段) 执行过程

- 如果表里只有主键索引没有二级索引：

  ![image-20240130104706636](C:\Users\Summerice\AppData\Roaming\Typora\typora-user-images\image-20240130104706636.png)

​		可以看到使用了主键索引。

- 当表中还有二级索引时：

  ![image-20240130104926658](C:\Users\Summerice\AppData\Roaming\Typora\typora-user-images\image-20240130104926658.png)

​		可以看到，当我们加了一个二级索引 name 后，再使用 count(主键) 实际扫描的是二级索引树，这是因为二级索引的叶子节点存储的是主键的值，并且聚簇索引还会存储一些额外的信息，所以 MySQL 查询优化器认为直接扫描二级索引树的效率更高，这与上一节 **MySQL索引失效场景** 中的两类特殊情况原因相同。

#### 3.count(1) 执行过程

- 没有二级索引时：

  ![image-20240130110249513](C:\Users\Summerice\AppData\Roaming\Typora\typora-user-images\image-20240130110249513.png)

  可以看到走的是主键索引。

- 有二级索引时：

  ![image-20240130110155754](C:\Users\Summerice\AppData\Roaming\Typora\typora-user-images\image-20240130110155754.png)

​		可以看到走的是二级索引，原因同上。

#### 4.count(*) 执行过程

同count(1)，因为 count(*) 中的 * 会被 MySQL 转化为数字 0，所以 count(*) 和 count(1) 是一模一样的。

#### 5.count(普通字段) 执行过程

![image-20240130110836304](C:\Users\Summerice\AppData\Roaming\Typora\typora-user-images\image-20240130110836304.png)

可以看到，走的是全表扫描，所以这种性能最差。

#### 6.为什么 count(1) 和 count(*) 比 count(主键字段) 性能更高？

很简单，从 count() 函数的定义就可以看出来，因为要判断参数是否为 NULL ，所以前两种参数都为确定的数字，肯定不为 NULL，而 count(主键字段)，需要从索引树中挨个取出主键值并判断是否为 NULL 后再进行计数，所以说，相比前两种，count(主键字段) 会多一个取主键值的操作，自然性能就低一点。

#### 7.大表查询如何优化 count(*) 或 count(1)

大表查询经常使用 count(*) 是很慢的。

- 取近似值：

  ​		如果业务对统计个数不要求十分精确的话，可以使用 explain 命令执行 count(*)，这样它并不会真正的去查询记录数，而使给出一个估算值，所以效率很高。

  ​		MySQL 在分析 SQL 时会生成一个查询计划，在这个计划中的查询优化阶段，查询优化器会根据统计信息和相关的数据库对象的元数据来进行估算。

- 用另张表存储记录数：

  ​		在主表增删操作的时候必须同步维护该表。