# gqueue

动态大小的并发安全队列。

使用方式：
```go
import "gitee.com/johng/gf/g/container/gqueue"
```

方法列表：godoc.org/github.com/johng-cn/gf/g/container/gqueue
```go
type IntQueue
    func NewIntQueue() *IntQueue
    func (q *IntQueue) Pop() int
    func (q *IntQueue) Push(v int)
    func (q *IntQueue) Size() int
type InterfaceQueue
    func NewInterfaceQueue() *InterfaceQueue
    func (q *InterfaceQueue) Pop() interface{}
    func (q *InterfaceQueue) Push(v interface{})
    func (q *InterfaceQueue) Size() int
type StringQueue
    func NewStringQueue() *StringQueue
    func (q *StringQueue) Pop() string
    func (q *StringQueue) Push(v string)
    func (q *StringQueue) Size() int
type UintQueue
    func NewUintQueue() *UintQueue
    func (q *UintQueue) Pop() uint
    func (q *UintQueue) Push(v uint)
    func (q *UintQueue) Size() int
```


gqueue与原生channel的性能测试：
```
john@johnstation:~/Workspace/Go/GOPATH/src/gitee.com/johng/gf/g/container/gqueue$ go test *.go -bench=".*"
goos: linux
goarch: amd64
BenchmarkGqueueNew1000W-8      	10000000	       131 ns/op
BenchmarkChannelNew1000W-8     	     200	   5886021 ns/op
BenchmarkGqueuePush-8          	10000000	       128 ns/op
BenchmarkGqueuePushAndPop-8    	20000000	       111 ns/op
BenchmarkChannelPushAndPop-8   	50000000	        39.3 ns/op
PASS
ok  	command-line-arguments	15.667s
```
可以看到原生的channel的读写性能是非常高的，但是创建的时候由于需要初始化内存，因此创建channel的时候效率非常非常低，并且受到队列大小的限制，写入的数据不能超过指定的队列大小。

gqueue使用起来比channel更加灵活，不仅创建效率高，不受队列大小限制(当然也可以指定大小)，并且可以像操作双向链表那样执行队列头尾操作。从基准测试结果中也可以看得到，相比较channel，这些灵活性都是靠牺牲了一定的效率来实现的。不过相比较于channel，gqueue的push&pop效率也是相当得优异！


文档未完待续。