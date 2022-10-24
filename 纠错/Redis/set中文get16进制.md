解决方法：

​		在启动redis时加上后缀 "--raw"，如下

```shell
summerice@summerice-ubuntu:~$ redis-cli --raw
127.0.0.1:6379> set 菜虚鲲
ERR wrong number of arguments for 'set' command

127.0.0.1:6379> get 菜虚鲲
鸡你太美
```

