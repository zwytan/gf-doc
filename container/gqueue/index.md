[TOC]

# gqueue

**动态大小**的并发安全队列。同时，`gqueue`也支持限制队列大小，固定队列大小时效率和标准库的`channel`无异。

**使用场景**：

该队列是并发安全的，常用于多`goroutine`数据通信且支持动态队列大小的场景。

`gqueue`安全队列与`glist`安全链表在使用上区别比较大：`gqueue`的读写操作是阻塞式的(当队列缓冲区满时，写操作会像`channel`那样阻塞等待)，`glist`没有阻塞效果；`gqueue`可以使用在`select`语法中，`glist`不能。

**使用方式**：
```go
import "gitee.com/johng/gf/g/container/gqueue"
```

**接口文档**：[godoc.org/github.com/gogf/gf/g/container/gqueue](https://godoc.org/github.com/gogf/gf/g/container/gqueue)
```go
type Queue
    func New(limit ...int) *Queue
    func (q *Queue) Close()
    func (q *Queue) Pop() interface{}
    func (q *Queue) Push(v interface{})
    func (q *Queue) Size() int
```

## 使用示例

```go
package main

import (
    "fmt"
    "time"
    "gitee.com/johng/gf/g/os/gtime"
    "gitee.com/johng/gf/g/container/gqueue"
)

func main() {
    q := gqueue.New()
    // 数据生产者，每隔1秒往队列写数据
    gtime.SetInterval(time.Second, func() bool {
        v := gtime.Now().String()
        q.Push(v)
        fmt.Println("Push:", v)
        return true
    })

    // 3秒后关闭队列
    gtime.SetTimeout(3*time.Second, func() {
        q.Close()
    })

    // 消费者，不停读取队列数据并输出到终端
    for {
        if v := q.Pop(); v != nil {
            fmt.Println(" Pop:", v)
        } else {
            break
        }
    }
}
```
在该示例中，第3秒时关闭队列，这时程序立即退出，因此结果中只会打印2秒的数据。
执行后，输出结果为：
```html
Push: 2018-09-07 14:03:00
 Pop: 2018-09-07 14:03:00
Push: 2018-09-07 14:03:01
 Pop: 2018-09-07 14:03:01
```

## gqueue与glist

`gqueue`的底层基于`list`链表实现动态大小特性，但是`gqueue`的使用场景都是多`goroutine`下的并发安全通信场景。在队列满时存储(限制队列大小时)，或者在队列空时读取数据会产生类似`channel`那样的阻塞效果。

`glist`是一个并发安全的链表，并可以允许在关闭并发安全特性的时和一个普通的`list`链表无异，在存储和读取数据时不会发生阻塞。


## gqueue与channel
`gqueue`与标准库`channel`的性能测试，其中每一次基准测试的`b.N`值均为`20000000`，以保证动态队列存取一致防止`deadlock`:
```html
goos: darwin
goarch: amd64
pkg: gitee.com/johng/gf/g/container/gqueue
Benchmark_Gqueue_StaticPushAndPop-4       20000000            84.2 ns/op
Benchmark_Gqueue_DynamicPush-4            20000000             164 ns/op
Benchmark_Gqueue_DynamicPop-4             20000000             121 ns/op
Benchmark_Channel_PushAndPop-4            20000000            70.0 ns/op
PASS

```
可以看到标准库的`channel`的读写性能是非常高的，但是创建的时候由于需要初始化内存，因此创建`channel`的时候效率非常非常低，并且受到队列大小的限制，写入的数据不能超过指定的队列大小。

`gqueue`使用起来比`channel`更加灵活，不仅创建效率高，不受队列大小限制(当然也可以指定大小)。从基准测试结果中也可以看得到，相比较`channel`，这些灵活性都是靠牺牲了一定的效率来实现的。

## 性能基准测试

```
goos: darwin
goarch: amd64
pkg: gitee.com/johng/gf/g/container/gqueue
Benchmark_Gqueue_StaticPushAndPop-4   	20000000	        97.7 ns/op
Benchmark_Gqueue_DynamicPush-4        	20000000	         183 ns/op
Benchmark_Gqueue_DynamicPop-4         	20000000	        96.7 ns/op
Benchmark_Channel_PushAndPop-4        	20000000	        75.8 ns/op
PASS
```



