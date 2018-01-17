
>[danger] # grpool

Go语言中的goroutine虽然相对于系统线程来说比较轻量级，但是在高并发量下的goroutine频繁创建和销毁对于性能损耗以及GC来说压力也不小。充分将goroutine复用，减少goroutine的创建/销毁的性能损耗，这便是grpool对goroutine进行池化封装的目的。例如，针对于100W个执行任务，使用goroutine的话需要不停创建并销毁100W个goroutine，而使用grpool也许底层只需要几千个goroutine便能充分复用地执行完成所有任务。经测试，在高并发下grpool的性能比原生的goroutine高出几倍到数百倍！并且随之也极大地降低了内存使用率。

性能测试报告：http://johng.cn/grpool-performance/

>[success] ## 方法列表
```go
func Add(f func())
func Jobs() int
func SetExpire(expire int)
func SetSize(size int)
func Size() int
type Pool
    func New(expire int, sizes ...int) *Pool
    func (p *Pool) Add(f func())
    func (p *Pool) Close()
    func (p *Pool) Jobs() int
    func (p *Pool) SetExpire(expire int)
    func (p *Pool) SetSize(size int)
    func (p *Pool) Size() int
```

通过grpool.New方法创建一个goroutine池，并给定池中goroutine的有效时间，单位为秒，第二个参数为非必需参数，用于限定池中的工作goroutine数量，默认为不限制。需要注意的是，任务可以不停地往池中添加，没有限制，但是工作的goroutine是可以做限制的。我们可以通过Size()方法查询当前的工作goroutine数量，使用Jobs()方法查询当前池中待处理的任务数量。同时，池的大小和goroutine有效期可以通过SetSize和SetExpire方法进行动态改变。

grpool包提供了默认的goroutine池，直接通过grpool.Add即可往默认的池中添加任务，任务参数必须是一个 **func()** 类型的函数/方法。

>[success] ## 使用示例

1、使用默认的goroutine池，限制10个工作goroutine执行1000个任务。
gitee.com/johng/gf/blob/master/geg/os/grpool/grpool1.go
```go
package main

import (
    "time"
    "fmt"
    "gitee.com/johng/gf/g/os/gtime"
    "gitee.com/johng/gf/g/os/grpool"
)

func job() {
    time.Sleep(1*time.Second)
}

func main() {
    grpool.SetSize(10)
    for i := 0; i < 1000; i++ {
        grpool.Add(job)
    }
    gtime.SetInterval(2*time.Second, func() bool {
        fmt.Println("size:", grpool.Size())
        fmt.Println("jobs:", grpool.Jobs())
        return true
    })
    select {}
}
```
这段代码中的任务函数的功能是sleep 1秒钟，这样便能充分展示出goroutine数量限制功能。其中，我们使用了gtime.SetInterval定时器每隔2秒钟打印出当前默认池中的工作goroutine数量以及待处理的任务数量。







    
    
    
    
    
    