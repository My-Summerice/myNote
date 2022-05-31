# Mutex

#### 临界区(Critical Section)

​		当程序并发的运行时，多个Go协程不应该同时访问能修改共享资源的代码。**这些修改共享资源的代码被成为临界区**。

经典的共享变量加一测试：

```go
package main  
import (  
    "fmt"
    "sync"
    )
var x  = 0  
func increment(wg *sync.WaitGroup) {  
    x = x + 1
    wg.Done()
}
func main() {  
    var w sync.WaitGroup
    for i := 0; i < 1000; i++ {
        w.Add(1)        
        go increment(&w)
    }
    w.Wait()
    fmt.Println("final value of x", x)
}
```

使用`go run -race 、go build -race或go test -race`

加上这个参数之后，编译器会对代码在执行时对所有共享变量的访问，如果发现一个 goroutine 写入一个变量之后，没有任何同步的操作，就有另外一个 goroutine 读写了这个变量，那就说明这里存在竞态，就会报错。

运行结果如下：

```go
==================
WARNING: DATA RACE
Read at 0x0000005d4400 by goroutine 8:
  main.increment()
      /home/summerice/go/src/Mutex/sharedVar.go:8 +0x30
  main.main·dwrap·1()
      /home/summerice/go/src/Mutex/sharedVar.go:15 +0x39

Previous write at 0x0000005d4400 by goroutine 7:
  main.increment()
      /home/summerice/go/src/Mutex/sharedVar.go:8 +0x48
  main.main·dwrap·1()
      /home/summerice/go/src/Mutex/sharedVar.go:15 +0x39

Goroutine 8 (running) created at:
  main.main()
      /home/summerice/go/src/Mutex/sharedVar.go:15 +0x64

Goroutine 7 (finished) created at:
  main.main()
      /home/summerice/go/src/Mutex/sharedVar.go:15 +0x64
==================
final value of x 999
Found 1 data race(s)
exit status 66
```

总结：**当并发了足够多的临界区代码时，可能会出现一个协程中的x在加一操作后还没来得及赋值就被别的协程先进行了临界操作，那么在这个期间进行的临界操作都是无效的，因为最后x的值都会被赋值为第一个协程加一操作后的值**。所以最后程序输出的结果可能是1～1000中的一个随机整数。



Mutex在“sync”包中,使用方法：

```go
var x = 0
f1 (m *sync.Mutex, wg *sync.WaitGroup) {
    m.Lock()
    x++
    m.Unlock()
    wg.Done()
}

func main() {
    var wg sync.WaitGroup
	var m sync.Mutex
    for {
        wg.Add(1)
        go f1(&m, &wg)
    }
    wg.Wait() 
}
```

处于Lock()和Unlock()之间的代码在任何时间都只能被一个协程执行（只允许一个协程执行这段代码）

**不仅可以用Mutex处理竞态条件，使用信道也可也可解决这个问题**