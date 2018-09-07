[TOC]

# gqueue

动态大小的并发安全队列。

**使用场景**：

该队列是并发安全的，常用于多`goroutine`数据通信的场景。

**使用方式**：
```go
import "gitee.com/johng/gf/g/container/gqueue"
```

**方法列表**：[godoc.org/github.com/johng-cn/gf/g/container/gqueue](https://godoc.org/github.com/johng-cn/gf/g/container/gqueue)
```go
type Queue
    func New(limit ...int) *Queue
    func (q *Queue) Close()
    func (q *Queue) PopBack() interface{}
    func (q *Queue) PopFront() interface{}
    func (q *Queue) PushBack(v interface{}) error
    func (q *Queue) PushFront(v interface{}) error
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
        q.PushBack(v)
        fmt.Println("PushBack:", v)
        return true
    })

    // 3秒后关闭队列
    gtime.SetTimeout(3*time.Second, func() {
        q.Close()
    })

    // 消费者，不停读取队列数据并输出到终端
    for {
        if v := q.PopFront(); v != nil {
            fmt.Println("PopFront:", v)
        } else {
            break
        }
    }
}
```
在该示例中，第3秒时关闭队列，这时程序立即退出，因此结果中只会打印2秒的数据。
执行后，输出结果为：
```html
PushBack: 2018-09-07 14:03:00
PopFront: 2018-09-07 14:03:00
PushBack: 2018-09-07 14:03:01
PopFront: 2018-09-07 14:03:01
```

## gqueue与glist

`gqueue`的底层基于`list`链表实现动态大小特性，但是`gqueue`的使用场景都是多`goroutine`下的并发安全通信场景。在队列满时存储(限制队列大小时)，或者在队列空时读取数据会产生类似`channel`那样的阻塞效果。

`glist`是一个并发安全的链表，并可以允许在关闭并发安全特性的时和一个普通的`list`链表无异，在存储和读取数据时不会发生阻塞。


## gqueue与channel
`gqueue`与原生`channel`的性能测试：
```html
john@johnstation:~/Workspace/Go/GOPATH/src/gitee.com/johng/gf/g/container/gqueue$ go test *.go -bench=".*"
goos: linux
goarch: amd64
BenchmarkGqueueNew1000W-8       10000000        131 ns/op
BenchmarkChannelNew1000W-8           200    5886021 ns/op
BenchmarkGqueuePush-8           10000000        128 ns/op
BenchmarkGqueuePushAndPop-8     20000000        111 ns/op
BenchmarkChannelPushAndPop-8    50000000       39.3 ns/op
PASS
ok   command-line-arguments 15.667s
```
可以看到原生的`channel`的读写性能是非常高的，但是创建的时候由于需要初始化内存，因此创建`channel`的时候效率非常非常低，并且受到队列大小的限制，写入的数据不能超过指定的队列大小。

`gqueue`使用起来比`channel`更加灵活，不仅创建效率高，不受队列大小限制(当然也可以指定大小)，并且可以像操作双向链表那样执行队列头尾操作。从基准测试结果中也可以看得到，相比较`channel`，这些灵活性都是靠牺牲了一定的效率来实现的。不过相比较于`channe`l，`gqueue`的`push&pop`效率也是相当得优异！

