>[danger] # gchan

优雅的Channel操作。

使用方式：
```go
import "gitee.com/johng/gf/g/container/gchan"
```

方法列表：godoc.org/github.com/johng-cn/gf/g/container/gchan

```go
type Chan
    func New(limit int) *Chan
    func (q *Chan) Close()
    func (q *Chan) Pop() interface{}
    func (q *Chan) Push(v interface{}) error
    func (q *Chan) Size() int
```


原生的channel操作在channel关闭后再向channel发送数据或者多次关闭channel会引发panic，gchan包使得channel的操作变得更加优雅，当然，这一部分优雅是需要牺牲一部分的操作性能来实现的。

gchan与原生channel的性能测试：
```
john@johnstation:~/Workspace/Go/GOPATH/src/gitee.com/johng/gf/g/container/gchan$ go test *.go -bench=".*"
goos: linux
goarch: amd64
BenchmarkGqueuePushAndPop-8    	20000000	        71.9 ns/op
BenchmarkChannelPushAndPop-8   	50000000	        39.3 ns/op
PASS
ok  	command-line-arguments	3.663s
```