# ubuntu安装redis-server

[toc]

#### 1.安装

```shell
sudo apt-get update
sudo apt-get install redis-server 
```

#### 2.启动

```shell
redis-server
```

#### 3.检验是否启动

```shell
redis-cli
```

正常结果如下：

```shell
summerice@summerice-ubuntu:~$ redis-server
34072:C 08 Aug 2022 14:52:56.390 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
34072:C 08 Aug 2022 14:52:56.390 # Redis version=5.0.7, bits=64, commit=00000000, modified=0, pid=34072, just started
34072:C 08 Aug 2022 14:52:56.390 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
34072:M 08 Aug 2022 14:52:56.390 * Increased maximum number of open files to 10032 (it was originally set to 1024).
34072:M 08 Aug 2022 14:52:56.390 # Could not create server TCP listening socket *:6379: bind: Address already in use
summerice@summerice-ubuntu:~$ redis-cli
127.0.0.1:6379> ping
PONG
127.0.0.1:6379> 
```

