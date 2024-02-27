## 开启VPN后Git仍旧很慢的解决办法（Windows）

1. 找到代理:网络与Internet > 代理> 编辑 >复制代理ip地址和端口 eg: http=127.0.0.1:7890;https=127.0.0.1:7890
2. 设置代理 可以同时执行多条命令 完成后就可以clone/pull了
3. git config --global http.proxy [替换成对应的ip地址与端口]
4. git config --global https.proxy [替换成对应的ip地址与端口]