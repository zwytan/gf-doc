# glist

并发安全双向列表。

**使用场景**：

并发安全场景下的链表操作，也可以关闭并发安全性当做普通的链表来使用。

**使用方式**：
```go
import "github.com/gogf/gf/g/container/glist"
```

**接口文档**：

https://godoc.org/github.com/gogf/gf/g/container/glist

## 性能基准测试

```
goos: darwin
goarch: amd64
pkg: github.com/gogf/gf/g/container/glist
Benchmark_PushBack-4    	20000000	       194 ns/op
Benchmark_PushFront-4   	20000000	       243 ns/op
Benchmark_Len-4         	20000000	        20.3 ns/op
Benchmark_PopFront-4    	20000000	        65.6 ns/op
Benchmark_PopBack-4     	20000000	        51.8 ns/op
PASS
```
