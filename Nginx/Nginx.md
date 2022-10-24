# Nginx

### 1.基础概念

#### 1.1 正向代理

在客户端（浏览器）中配置代理服务器，通过代理服务器进行互联网访问。

#### 1.2 反向代理

在访问时，我们只需要将请求发送到反向代理服务器，由反向代理服务器去选择目标服务器并获取数据，然后返回给客户端，此时反向代理服务器和目标服务器对外来讲就是一个服务器，暴露的是反向代理服务器的地址，而隐藏了真实服务器的IP地址。

#### 1.3 负载均衡

当单个服务器解决不了访问压力时，我们增加服务器的数量，然后通过反向代理服务器（Nginx）将请求分发到各个服务器上，将原先请求集中到单个服务器上的情况改为请求分发到多个服务器上，将负载分发到不同的服务器，也就是我们所说的负载均衡。

#### 1.4 动静分离

为了加快网站的解析速度，可以把动态页面和静态页面由不同服务器来解析，加快解析速度。降低原来单个服务器的压力。

---

### 2.nginx的安装

`sudo apt-get install nginx`

https://www.cnblogs.com/zhang1f/p/15771853.html

---

### 3.常用命令

#### 3.1 启动

- 先启动服务：`service nginx start`

- `sudo nginx`
- 查询是否启动：`ps aux | grep nginx`

#### 3.2 停止

- `sudo nginx -s stop`立即停止

- `sudo nginx -s quit`等进程完成当前工作后停止
- `sudo killall nginx`直接杀死所有进程，当 Nginx 报错时可以使用

- 可以关闭服务：`service nginx stop`，若 Nginx 进程未结束时关闭了服务，再操作 Nginx 会报错，此时就应该先用 kill 命令杀死进程，然后先开启服务再启动 Nginx

#### 3.3 重新载入配置文件

- `sudo nginx -s reload`

#### 3.4 查看端口号

- `sudo netstat -tlnp | grep nginx`

---

### 4.Nginx目录结构

#### 4.1 内容

- `/var/www/html` 实际的 Web 内容。默认请看下只有 Nginx 自己的原生页面。我们可以通过更改 Nginx 配置来更改文件。

#### 4.2 服务器配置

- `/etc/nginx` Nginx 配置目录。所有 Nginx 的配置文件都在这里。
- `/etc/nginx/nginx.conf` Nginx 的配置文件。大多数全局配置可以通过这个文件来修改。
- `/etc/nginx/sites-available` 用来存储服务器下每个站点服务器块的目录。 默认情况下 Nginx 不会直接使用目录下的配置文件，需要我们更改配置来告诉 Nginx 来去读。
- `/etc/nginx/sites-enabled`这里是存储已经启用站点服务器块的目录。
- `/etc/nginx/snippets` 这个目录包含一些 Nginx 的配置文件。可打开详细查看这些配置文件到文档进行学习。

#### 4.3 服务器日志

- `/var/log/nginx/access.log` 这里是 Nginx 到日志文件，对 Web 服务器的每个请求都会记录在这个日志中。
- `/var/log/nginx/error.log` 记录 Nginx 运行过程中发生的错误日志。

---

### 5.Nginx配置文件

#### 5.1 配置文件位置

```shell
/etc/nginx/nginx.conf
```

#### 5.2 配置文件组成

（1）nginx 配置文件由三部分组成：

第一部分：全局块

​	从配置文件开始到 envents 块之间的所有内容，主要会设置一些影响 nginx 服务器整体运行的配置指令。

```shell
user www-data;
worker_processes auto;		// Nginx 并发处理的数量
pid /run/nginx.pid;
include /etc/nginx/modules-enabled/*.conf;
```

第二部分：events 块

​	events 块的指令主要影响 Nginx 服务器与用户的网络连接,该部分对 nginx 的性能影响较大，在使用中应灵活配置。

```shell
events {
    worker_connections 768;		// 每个 worker_process 支持的最大连接数
    # multi_accept on;
}
```

第三部分：http 块

```shell
http {

	##
	# Basic Settings
	##

	sendfile on;
	tcp_nopush on;
	tcp_nodelay on;
	keepalive_timeout 65;
	types_hash_max_size 2048;
	# server_tokens off;

	# server_names_hash_bucket_size 64;
	# server_name_in_redirect off;

	include /etc/nginx/mime.types;
	default_type application/octet-stream;

	##
	# SSL Settings
	##

	ssl_protocols TLSv1 TLSv1.1 TLSv1.2 TLSv1.3; # Dropping SSLv3, ref: POODLE
	ssl_prefer_server_ciphers on;

	##
	# Logging Settings
	##

	access_log /var/log/nginx/access.log;
	error_log /var/log/nginx/error.log;

	##
	# Gzip Settings
	##

	gzip on;

	# gzip_vary on;
	# gzip_proxied any;
	# gzip_comp_level 6;
	# gzip_buffers 16 8k;
	# gzip_http_version 1.1;
	# gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;

	##
	# Virtual Host Configs
	##

	include /etc/nginx/conf.d/*.conf;
	include /etc/nginx/sites-enabled/*;
}
```

我们发现其中这样的语句：   include /etc/nginx/sites-enabled/*   可以看出/etc/nginx/sites-enabled/default文件也是一个核心的配置文件，其中包含了主要的配置信息， 如服务器跟目录、服务器名称、location信息和server信息。

```shell
server {
	listen 80 default_server;
	listen [::]:80 default_server;

// 此处注释过多已省略，详见 /etc/nginx/sites-enabled/default

	root /var/www/html;

	# Add index.php to the list if you are using PHP
	index index.html index.htm index.nginx-debian.html;

	server_name _;

	location / {
		# First attempt to serve request as file, then
		# as directory, then fall back to displaying a 404.
		try_files $uri $uri/ =404;
	}

```

参考：

​	1.[Ubuntu中Nginx的安装与配置详细说明](http://t.zoukankan.com/XuanZP-p-13750446.html)

​	2.[Ubuntu安装配置Nginx](https://www.cnblogs.com/zhang1f/p/15771853.html##)（主要）

---

### 6.Nginx配置实例

#### 6.1 反向代理

。。。。。暂无
