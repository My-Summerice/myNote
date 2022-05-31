# defer

含有defer语句的函数或方法会在该函数或方法即将返回之前调用defer指定的函数或方法。

**注意**：

​		defer语句虽然是在函数将返回之前调用，但其实在执行defer语句时defer指定的函数或方法的实参就已确定。

例：

```go
package main

import (  
    "fmt"
)

func printA(a int) {  
    fmt.Println("value of a in deferred function", a)
}
func main() {  
    a := 5
    defer printA(a)
    a = 10
    fmt.Println("value of a before deferred function call", a)

}
```

输出：

```go
value of a before deferred function call 10  
value of a in deferred function 5
```



#### defer栈

在一个函数中多次调用defer时Go会将defer调用压栈，按照后进先出LIFO（Last In First Out）原则执行。



#### defer的实际应用

当一个函数应该在与当前代码流（Code Flow）无关的环境下调用时，可以使用 `defer`。

```go
package main

import (  
    "fmt"
    "sync"
)

type rect struct {  
    length int
    width  int
}

func (r rect) area(wg *sync.WaitGroup) {  
    if r.length < 0 {
        fmt.Printf("rect %v's length should be greater than zero\n", r)
        wg.Done()
        return
    }
    if r.width < 0 {
        fmt.Printf("rect %v's width should be greater than zero\n", r)
        wg.Done()
        return
    }
    area := r.length * r.width
    fmt.Printf("rect %v's area %d\n", r, area)
    wg.Done()
}

func main() {  
    var wg sync.WaitGroup
    r1 := rect{-67, 89}
    r2 := rect{5, -67}
    r3 := rect{8, 9}
    rects := []rect{r1, r2, r3}
    for _, v := range rects {
        wg.Add(1)
        go v.area(&wg)
    }
    wg.Wait()
    fmt.Println("All go routines finished executing")
}
```

**如果你仔细观察，会发现 `wg.Done()` 只在 `area` 函数返回的时候才会调用。`wg.Done()` 应该在 `area` 将要返回之前调用，并且与代码流的路径（Path）无关，因此我们可以只调用一次 `defer`，来有效地替换掉 `wg.Done()` 的多次调用**。

