## go mod

历程及操作方法：（非常详细！！！）

https://blog.csdn.net/qq_42950328/article/details/117786102?spm=1001.2101.3001.6650.1&utm_medium=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-117786102-blog-123954222.pc_relevant_paycolumn_v3&depth_1-utm_source=distribute.pc_relevant.none-task-blog-2%7Edefault%7ECTRLIST%7ERate-1-117786102-blog-123954222.pc_relevant_paycolumn_v3&utm_relevant_index=2



### go mod 操作方法

#### 1. 新建项目

```
mkdir XXX or touch XXX
```

#### 2.初始化

```go
cd [需要导入依赖的文件所在的目录]
go mod init module-[需要导入依赖的文件名]
go mod init [包名]	//也可以，2选1
```

#### 3.导入依赖包

```go
go mod tidy
```

- 该命令会导入依赖包并删除不需要的包

- 还有一个 go install 命令也可以导入依赖，实质上是使用 go get 导入的

- 提前打开mod模式并更改下载路径为国内镜像

	```go
	go env -w GO111MODULE="on"
	go env -w GOPROXY="https://goproxy.cn"	//七牛云镜像
	```

	
