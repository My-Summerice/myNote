# 工作池

一般而言，工作池就是一组等待任务分配的协程。一旦完成了所分配的任务，这些线程可继续等待任务的分配。  

我们会使用缓冲信道来实现工作池。**我们工作池的任务是计算所输入数字的每一位的和**。例如，如果输入 234，结果会是 9（即 2 + 3 + 4）。向工作池输入的是一列伪随机数。  

我们工作池的核心功能如下：  

- 创建一个 Go 协程池，监听一个等待作业分配的输入型缓冲信道。  
- 将作业添加到该输入型缓冲信道中。  
- 作业完成后，再将结果写入一个输出型缓冲信道。  
- 从输出型缓冲信道读取并打印结果。  

```go
import (
    "fmt"
    "math/rand"
    "sync"
    "time"
)

// 创建结构体，用来表示作业和结果
type Job struct {
    id int
    randomno int
}

type Result struct{
    job Job
    randomno int
}

// 创建用于读取作业和写入结果的缓冲信道
var jobs = make(chan Job, 10)
var results = make(chan Result, 10)

// 创建用于完成作业的协程
func digits(numbers int) int {
    sum := 0
    no := numbers
    for no != 0 {
        sum += no % 10
        no /= 10
    }
    return sum
}

// 创建worker协程监听jobs中的作业，一旦工作协程完成了当前作业，就将其结果写入results
func worker(wg *sync.WaitGroup) {
    for job := range jobs {
        output := Result{job, digits(job.randomno)} // 创建结构体用于存放job的结果
        results <- output
    }
    wg.Done()
}

// 创建worker协程的工作池以达到并发地完成jobs中作业的效果
func creatWorkerPool(noOfWorkers int) {
    var wg sync.WaitGroup
    for i := 0; i < noOfWorkers; i++ {
        wg.Add(1)
        go worker(&wg) 
    }
    wg.Wait()
    close(results)
}

// 创建allocate协程向jobs写入作业
func allocate(noOfJobs int) {
    for i := 0; i < noOfJobs; i++ {
        randomno := rand.Intn(999)
        job := Job{i, randomno}
        jobs <- job
    }
    close(jobs)
}

// 创建result协程监听并打印results中的结果
func result(done chan bool){
    for result := range results {
        //time.Sleep(1 * time.Second)// 模拟耗时场景
        fmt.Printf("Job id %d, input random no %d, sum of digits %d\n", result.job.id, result.job.randomno, result.randomno)
    }
    done <- true
}

//文中的第67、80行注释是为了测试go result(done)中的done是否可以去掉，
//测试结果：不可以去掉，当给result()中设置模拟耗时场景时可从输出结果看出并未输出或未
//输出完整的results,因为result()并发执行时没有done信道阻塞主协程main()，所以当main()//正常执行完毕退出时result()仍为完全打印出所有results
//在加入done后即使模拟result()耗时场景也会因为done阻塞主协程而强制等待result()完全输出

func main(){
    startTime := time.Now()
    jobs := 100 //修改100为10减少不必要耗时
    go allocate(jobs)
    done := make(chan bool)
    go result(done)
    workers := 10
    creatWorkerPool(workers) //可以不go此函数,即使go了，下一行代码还是处于阻塞main协程的状态
    <- done
    endTime := time.Now()
    diff := endTime.Sub(startTime)
    fmt.Println("total time taken ",diff.Seconds(), "seconds") //打印总耗时
}
```



