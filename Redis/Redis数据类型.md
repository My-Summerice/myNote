# Redis数据类型

总共五种：string（字符串）、hash（哈希）、list（列表）、set（集合）、zset（sort set：有序集合）。

---

### String（字符串）

- string 是 redis 最基本的类型，一个 Key 对应一个 Value。

- string 类型是二进制安全的。即 string 可以包含任何数据。比如jpg图片或者序列化的对象。
- string 类型最大能存储 512MB。

例：

```shell
127.0.0.1:6379> set 菜虚鲲 "鸡你太美"
OK
127.0.0.1:6379> get 菜虚鲲
鸡你太美
```

如上 Key 为 菜虚鲲 ，Value 为 鸡你太美，这里的 Value 即为 string 类型。

---

### Hash（哈希）

- hash 类型是一个键值对集合（key=>value）。

- hash 类型是一个 string 类型的 filed 和 value 的映射表，hash 特别适合用于存储对象。

例：

**DEL  [keyname]**用于删除之前测试用过的 Key，不然会报错。

```shell
127.0.0.1:6379> hmset summerice field1 "唱" field2 "跳"
WRONGTYPE Operation against a key holding the wrong kind of value

```

以下是正常操作

```shell
127.0.0.1:6379> del summerice
1
127.0.0.1:6379> hmset summerice field1 "唱" field2 "跳"
OK
127.0.0.1:6379> hget summerice
ERR wrong number of arguments for 'hget' command

127.0.0.1:6379> hget summerice field1
唱
127.0.0.1:6379> hget summerice field2
跳
```

如上 HMSET 用于设置多个 fileld=>value 对，HGET 获取对应 field 对应的 alue。

每个 hash 可以存储 2^32-1 键值对（40多亿）。

---

### List（列表）

list 类型是简单的字符串列表，按照插入顺序排序。可以对一个列表进行头插或尾插一个元素。

例：

```shell
127.0.0.1:6379> rpush 纯路人 rap 篮球
2
127.0.0.1:6379> lpush 纯路人 跳 唱
4
127.0.0.1:6379> lrange 纯路人 0 9
唱
跳
rap
篮球
```

列表最多可存储 2^32-1 个元素（40多亿）。

不可以对 string 类型进行列表操作，会报错，如下：

```shell
127.0.0.1:6379> get 菜虚鲲
鸡你太美
127.0.0.1:6379> rpush 菜虚鲲 666
WRONGTYPE Operation against a key holding the wrong kind of value
```

---

### Set（集合）

redis 的 set 是 string 类型的无序集合。

集合是通过哈希表实现的，所以添加、删除、查找的复杂度都是O(1)。

**sadd命令**

```shell
sadd key member
```

添加一个 string 元素到 key 对应的 set 集合中，返回成功添加的元素个数，因为集合元素的唯一性，重复的元素在添加时会被忽略。

例：

```shell
127.0.0.1:6379> sadd 小黑子 琛总
1
127.0.0.1:6379> sadd 小黑子 Tom老师
1
127.0.0.1:6379> sadd 小黑子 yaw自律 卓卓
2
127.0.0.1:6379> smembers 小黑子
卓卓
Tom老师
琛总
yaw自律
127.0.0.1:6379> sadd 小黑子 琛总
0
127.0.0.1:6379> smembers 小黑子
卓卓
Tom老师
琛总
yaw自律
```

集合中能存储的最大元素数同样为2^32-1（40多亿）。

---

### Zset（sorted set：有序集合）

zset 和 set 的不同：

- zset 中的每个元素均会关联一个**double类型**的分数，redis 通过这些分数对集合中的元素进行从小到大的排序。
- zset 中的元素唯一，但分数(score)却可以重复。

**zadd命令**

添加一个元素到集合中，若元素存在则更新对应的 score 。

```shell
zadd key score member
```

例：

```shell
127.0.0.1:6379> zadd ikun 0 黄屌
1
127.0.0.1:6379> zadd ikun 0.1 李某
1
127.0.0.1:6379> zadd ikun 0.2 龙文
1
127.0.0.1:6379> smembers ikun
WRONGTYPE Operation against a key holding the wrong kind of value

127.0.0.1:6379> zrangebyscore ikun 0 100
黄屌
李某
龙文
127.0.0.1:6379> zadd ikun 6 黄屌
0
127.0.0.1:6379> zrangebyscore ikun 0 100
李某
龙文
黄屌
```

