### 1.预先创建好需要使用的数据库和表

```mysql
create database test;

CREATE TABLE `person` (
    `user_id` int(11) NOT NULL AUTO_INCREMENT,
    `username` varchar(260) DEFAULT NULL,
    `sex` varchar(260) DEFAULT NULL,
    `email` varchar(260) DEFAULT NULL,
    PRIMARY KEY (`user_id`)
  ) ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8;

CREATE TABLE place (
    country varchar(200),
    city varchar(200),
    telcode int
)ENGINE=InnoDB AUTO_INCREMENT=2 DEFAULT CHARSET=utf8;
```

### 2.依赖

```go
import (
	_ "github.com/go-sql-driver/mysql"
    "github.com/jmoiron/sqlx"
)
```

详见go mod笔记

### 3.文件目录

```go
summerice@summerice-ubuntu:~/goWorkSpace/gopher/MySQL$ tree
.
├── delete.go
├── go.mod
├── go.sum
├── insert.go
├── main.go
├── select.go
└── update.go
```

此处可上传github链接：

### 4.主要函数

- sqlx.Open("选用的数据库", "用户名:密码@tcp(127.0.0.1:3306)/数据库名?charset=utf8")

```go
sqlx.Open("mysql", "root:summerice@tcp(127.0.0.1:3306)/test?charset=utf8")
```

- db.Exec(sql语句, 语句中对应'?'需要填写的参数...)

```go
str := "update person set user_name=? where user_id=?"

up, err := db.Exec(str, name, id)
```

### 5.mysql事务操作

mysql事务特性：

- 原子性
- 一致性
- 隔离性
- 持久性

golang MySQL事务应用：

```go
import (“github.com/jmoiron/sqlx")
Db.Begin()        开始事务
Db.Commit()        提交事务
Db.Rollback()     回滚事务 
```

操作代码：

```go
func main() {
        conn, err := Db.Begin()
        if err != nil {
            fmt.Println("begin failed :", err)
            return
        }

        r, err := conn.Exec("insert into person(username, sex, email)values(?, ?, ?)", "stu001", "man", "stu01@qq.com")
        if err != nil {
            fmt.Println("exec failed, ", err)
            conn.Rollback()
            return
        }
        id, err := r.LastInsertId()
        if err != nil {
            fmt.Println("exec failed, ", err)
            conn.Rollback()
            return
        }
        fmt.Println("insert succ:", id)

        r, err = conn.Exec("insert into person(username, sex, email)values(?, ?, ?)", "stu001", "man", "stu01@qq.com")
        if err != nil {
            fmt.Println("exec failed, ", err)
            conn.Rollback()
            return
        }
        id, err = r.LastInsertId()
        if err != nil {
            fmt.Println("exec failed, ", err)
            conn.Rollback()
            return
        }
        fmt.Println("insert succ:", id)

        conn.Commit()
    }
```

