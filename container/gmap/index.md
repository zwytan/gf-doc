# gmap

并发安全Map，最常用的并发安全数据结构。

使用场景：

需要并发安全支持的map数据类型场景。例如，map对象会被多个goroutine读写时。

使用方式：
```go
import "gitee.com/johng/gf/g/container/gmap"
```

方法列表：[godoc.org/github.com/johng-cn/gf/g/container/gmap](https://godoc.org/github.com/johng-cn/gf/g/container/gmap)

由于`gmap`包下的方法比较多，这里便不一一列举。`gmap`下包含了多种数据类型的map，可以使用 `gmap.New*` 方法来创建。


go语言从1.9版本开始引入了并发安全的`sync.Map`，我们来看看基准测试结果：
```shell
john@johnstation:~/Workspace/Go/GOPATH/src/gitee.com/johng/gf/g/container/gmap$ go test *.go -bench=".*"
goos: linux
goarch: amd64
BenchmarkGmapSet-8        	10000000	       181 ns/op
BenchmarkSyncmapSet-8     	 5000000	       366 ns/op
BenchmarkGmapGet-8        	30000000	        82.6 ns/op
BenchmarkSyncmapGet-8     	20000000	        95.7 ns/op
BenchmarkGmapRemove-8     	20000000	        69.8 ns/op
BenchmarkSyncmapRmove-8   	20000000	        93.6 ns/op
PASS
ok  	command-line-arguments	27.950s
```
