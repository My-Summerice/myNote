# curl [url]出错，但是浏览器访问没错

#### 描述：

```shell
summerice@summerice-ubuntu:~/goWorkSpace$ curl http://localhost:8888/man?age=11&name=summerice&birthday=2001-03-25
[1] 36414
[2] 36415
summerice@summerice-ubuntu:~/goWorkSpace$ Key: 'Person.Name' Error:Field validation for 'Name' failed on the 'required' tag

[1]-  Done                    curl http://localhost:8888/man?age=11
[2]+  Done                    name=summerice
```

但是使用浏览器直接访问网址不会出错。

#### 原因：

​	在linux中 '&' 字符表示后台运行，当执行到 '&' 时，后面的字符串就会被忽略掉。

#### 解决办法：

​	给 url 加上 ‘’ 或 “” ，如下：

```shell
summerice@summerice-ubuntu:~/goWorkSpace$ curl "http://localhost:8888/man?age=11&name=summerice&birthday=2001-03-25"
main.Person{Age:11, Name:"summerice", Birthday:time.Date(2001, time.March, 25, 0, 0, 0, 0, time.UTC)}[1]-  Done                    curl http://localhost:8888/man?age=11
[2]+  Done                    name=summerice
```

注：此处运行的代码为https://github.com/My-Summerice/goWorkSpace/gopher/Gin/parameterValidation/struct.go



