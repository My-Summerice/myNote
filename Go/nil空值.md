# nil

#### nil标识符不可与自身比较

即：`nil == nil`

报错：

```go
invalid operation: nil == nil (operator == not defined on nil)
```



#### nil是预定义好的标识符不是关键字或保留字

```go
var nil = errors.New("my god")
```

可以但不建议



#### nil没有默认类型

使用%T格式化输出会报错：

```go
use of untyped nil
```

==我自己测试输出的是：**<nil>**==



#### 不同类型nil的指针都是一样的

即所有默认零值为nil的变量对应的指针都为0x0



#### 不同类型的nil不可比较

比如：

```go
var m map[string]int
var p *int
fmt.Println(m == p)
```

报错：

```go
invalid operation: m == p (mismatched types map[string]int and *int)
```



#### 相同类型的nil也可能无法比较

比如：

```go
var s1 []int
var s2 []int
fmt.Println(s1 == s2)
```

报错：

```go
invalid operation: s1 == s2 (slice can only be compared to nil)
```

**Go中 map、slice、function类型的nil值不可互相比较，编译无法通过。但可以和nil标识符进行比较**



#### nil 是 ==map、slice、func、==pointer、channel、interface的零值

注意：%#v将会打印出该变量的类型和默认值，若是结构体则会在值前添加字段名

例：

```go
package main

import "fmt"

func main(){
    type Human struct{
        Name string
    }
    var people = Human{Name: "zhangsan"}
    fmt.Printf(" %T\n %v\n %+v\n %#v\n", people, people, people, people)
}
su
```

输出：

```go
 main.Human
 {zhangsan}
 {Name:zhangsan}
 main.Human{Name:"zhangsan"}
```



#### 不同类型的nil值占用的内存大小·可能不一样

一个类型的所有的值的内存布局都是一样的，nil 也不例外，nil 的大小与同类型中的非 nil 类型的大小是一样的。但是不同类型的 nil 值的大小可能不同。

```go
package main
import ( 
    "fmt"
    "unsafe"
)
func main() {
    var p *struct{}
    fmt.Println( unsafe.Sizeof( p ) ) // 8
    var s []int
    fmt.Println( unsafe.Sizeof( s ) ) // 24
    var m map[int]bool
    fmt.Println( unsafe.Sizeof( m ) ) // 8
    var c chan string
    fmt.Println( unsafe.Sizeof( c ) ) // 8
    var f func()
    fmt.Println( unsafe.Sizeof( f ) ) // 8
    var i interface{}
    fmt.Println( unsafe.Sizeof( i ) ) // 16
}
```

具体的大小取决于编译器和架构，上面打印的结果是在 64 位架构和标准编译器下完成的，对应 32 位的架构的，打印的大小将减半。







