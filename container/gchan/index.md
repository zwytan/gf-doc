# gchan

优雅的Channel操作。原生的`channel`操作在`channel`关闭后再向`channel`发送数据，或者多次关闭`channel`会引发`panic`，`gchan`包使得`channel`的操作变得优雅，当然，这一部分优雅是需要牺牲一部分的操作性能来实现的。`gchan`的使用方式同标准库的`chan`。

**使用场景**：

所有使用`chan`的场景都可使用`gchan`替代。

**使用方式**：
```go
import "gitee.com/johng/gf/g/container/gchan"
```

**方法列表**：[godoc.org/github.com/johng-cn/gf/g/container/gchan](https://godoc.org/github.com/johng-cn/gf/g/container/gchan)

```go
type Chan
    func New(limit int) *Chan
    func (q *Chan) Close()
    func (q *Chan) Pop() interface{}
    func (q *Chan) Push(v interface{}) error
    func (q *Chan) Size() int
```

`gchan`与原生`channel`的性能测试：
```html
john@johnstation:~/Workspace/Go/GOPATH/src/gitee.com/johng/gf/g/container/gchan$ go test *.go -bench=".*"
goos: linux
goarch: amd64
BenchmarkGchanPushAndPop-8    	20000000	        71.9 ns/op
BenchmarkChannelPushAndPop-8   	50000000	        39.3 ns/op
PASS
ok  	command-line-arguments	3.663s
```
