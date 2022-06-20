# strings.SplitN()

strings.SplitN() 用于将给定的字符串拆分为由分隔符分隔的子字符串并返回这些子字符串。

**用法**：

```go
func SplitN(s, sep string, n int) []string
```

s为待分割字符串，sep为分隔符，n为返回的字符串数。

- 若s不包含sep，则返回原切片s。
- 若sep为空，则在每个UTF-8序列之后拆分。
- 若s和sep均为空，则返回一个空切片。

- 若n == 0：返回一个空切片。
- 若n > 0：**最多**返回n个子字符串，最后一个字符串为未分割的剩余字符串。
- 若n < 0：将返回所有可能的子字符串。

代码：

```go
package main

import (
	"fmt"
	"strings"
)

func main() {
	// no sep in s
	s := strings.SplitN("a,b,c,d", "!", 4)
	fmt.Println(s)

	// sep == nil
	s = strings.SplitN("a,b,c,d", "", 4)
	fmt.Println(s)

	// sep == nil, s == nil
	s = strings.SplitN("", "", 4)
	fmt.Println(s)

	// n == 0
	s = strings.SplitN("a,b,c,d", ",", 0)
	fmt.Println(s)

	// n > 0 
	s = strings.SplitN("a,b,c,d", ",", 3)
	fmt.Println(s)

	// n < 0 
	s = strings.SplitN("a,b,c,d", ",", -1)
	fmt.Println(s)
}
```

输出：

```go
[a,b,c,d]
[a , b ,c,d]
[]
[]
[a b c,d]
[a b c d]
```

