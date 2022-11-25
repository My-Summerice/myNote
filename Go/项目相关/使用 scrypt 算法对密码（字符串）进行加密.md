# 使用 scrypt 算法对密码（字符串）进行加密

### 1.简介

​		scrypt是由著名的FreeBSD黑客 Colin Percival为他的备份服务 Tarsnap开发的。scrypt不仅计算所需时间长，而且占用的内存也多，使得并行计算多个摘要异常困难，因此利用rainbow table进行暴力攻击更加困难。

### 2.在Go中使用 scrypt

​		go 标准库中内置有 scrypt 包，我们只需直接声明并调用其中相关函数即可快速使用，接下来简单介绍一下：

- **func Key**

	`func Key(password, salt []byte, N, r, p, keyLen int) ([]byte, error)`

	其中，**password**是你需要进行加密的字符串，**salt**是一个你自己定义的盐（一个固定的字符串，在这里是一个byte类型的切片），**N**是用来设置内存开销的一个参数（数值越大，对你服务器的性能要求越高，所以破解的难度也会越大）并且N必须为一个大于1的二次幂数（2、4、8、16...），**r和p**必须遵循r*p < $2^{30}$的规则，**keyLen**代表加密后的字符串的长度，如果以上规则没有被遵循，则函数会返回一个空切片和错误。

	示例：

	```go
	func ScryptPwd(password string) string {
		keyLen := 10
		salt := []byte{10, 3, 52, 137, 90, 2, 255, 71}
		bytePwd, err := scrypt.Key([]byte(password), salt, 16384, 8, 1, keyLen)
		if err != nil {
			log.Fatal(err)
		}
		return base64.StdEncoding.EncodeToString(bytePwd)
	}
	```

	在数据库中查看存储的密码：

	```shell
	+------------------+
	| pass_word        |
	+------------------+
	| 6S/UD3X5Mc18tg== |
	+------------------+
	```

	可以看到加密成功。

