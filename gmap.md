>[danger] # gmap

并发安全Map。

使用方式：
```go
import "gitee.com/johng/gf/g/container/gmap"
```

方法列表：godoc.org/github.com/johng-cn/gf/g/container/gmap


go语言从1.9版本开始引入了并发安全的sync.Map，我们来看看基准测试结果：
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