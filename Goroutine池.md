
>[danger] # grpool

Go语言中的goroutine虽然相对于系统线程来说比较轻量级，但是在高并发量下的goroutine频繁创建和销毁对于性能损耗以及GC来说压力也不小。充分将goroutine复用，减少goroutine的创建/销毁的性能损耗，这便是grpool对goroutine进行池化封装的目的。例如，针对于100W个执行任务，使用goroutine的话需要不停创建并销毁100W个goroutine，而使用grpool也许底层只需要几千个goroutine便能充分复用地执行完成所有任务。经测试，在高并发下grpool的性能比原生的goroutine高出几倍到数百倍！并且随之也极大地降低了内存使用率。

性能测试报告：http://johng.cn/grpool-performance/

使用方式：
```go
import "gitee.com/johng/gf/g/os/grpool"
```

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

通过grpool.New方法创建一个goroutine池，并给定池中goroutine的有效时间，单位为**秒**，第二个参数为非必需参数，用于限定池中的工作goroutine数量，默认为不限制。需要注意的是，任务可以不停地往池中添加，没有限制，但是工作的goroutine是可以做限制的。我们可以通过Size()方法查询当前的工作goroutine数量，使用Jobs()方法查询当前池中待处理的任务数量。

同时，池的大小和goroutine有效期可以通过SetSize和SetExpire方法在运行时进行动态改变。这一点特性使得协程池的管理更加灵活，但是也增加了一定的维护风险，因为你无法得知池的属性在哪个地方被执行了修改。

同时，为便于使用，grpool包提供了默认的goroutine池，直接通过grpool.Add即可往默认的池中添加任务，任务参数必须是一个 **func()** 类型的函数/方法。

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
这段程序中的任务函数的功能是sleep 1秒钟，这样便能充分展示出goroutine数量限制功能。其中，我们使用了gtime.SetInterval定时器每隔2秒钟打印出当前默认池中的工作goroutine数量以及待处理的任务数量。

2、我们再来看一个新手经常容易出错的例子

gitee.com/johng/gf/blob/master/geg/os/grpool/grpool2.go

```go
package main

import (
    "fmt"
    "sync"
    "gitee.com/johng/gf/g/os/grpool"
)

func main() {
    wg := sync.WaitGroup{}
    for i := 0; i < 10; i++ {
        wg.Add(1)
        grpool.Add(func() {
            fmt.Println(i)
            wg.Done()
        })
    }
    wg.Wait()
}
```
我们这段代码的目的是要顺序地打印出0-9，然而运行后却输出：
```
10
10
10
10
10
10
10
10
10
10
```
为什么呢？这里的执行结果无论是采用go关键字来执行还是grpool来执行都是如此。原因是，对于异步线程/协程来讲，函数进行进行异步执行注册时，该函数并未真正开始执行(注册时只在goroutine的栈中保存了变量i的内存地址)，而一旦开始执行时函数才会去读取变量i的值，而这个时候变量i的值已经自增到了10。
清楚原因之后，改进方案也很简单了，就是在注册异步执行函数的时候，把当时变量i的值也一并传递获取；或者把当前变量i的值赋值给一个不会改变的临时变量，在函数中使用该临时变量而不是直接使用变量i。

改进后的示例代码如下：

1)、使用go关键字

gitee.com/johng/gf/blob/master/geg/os/grpool/grpool3.go

```go
package main

import (
    "fmt"
    "sync"
)

func main() {
    wg := sync.WaitGroup{}
    for i := 0; i < 10; i++ {
        wg.Add(1)
        go func(v int){
            fmt.Println(v)
            wg.Done()
        }(i)
    }
    wg.Wait()
}
```
执行后，输出结果为：
```
9
0
1
2
3
4
5
6
7
8
```
注意，异步执行时并不会保证按照函数注册时的顺序执行，以下同理。

2)、使用临时变量

gitee.com/johng/gf/blob/master/geg/os/grpool/grpool4.go

```go
package main

import (
    "fmt"
    "sync"
    "gitee.com/johng/gf/g/os/grpool"
)

func main() {
    wg := sync.WaitGroup{}
    for i := 0; i < 10; i++ {
        wg.Add(1)
        v := i
        grpool.Add(func() {
            fmt.Println(v)
            wg.Done()
        })
    }
    wg.Wait()
}

```
执行后，输出结果为：
```
9
0
1
2
3
4
5
6
7
8
```

这里可以看到，使用grpool进行任务注册时，只能使用func()类型的参数，因此无法在任务注册时把变量i的值注册进去，因此只能采用临时变量的形式来传递当前变量i的值。
    
    
    
    
    
    