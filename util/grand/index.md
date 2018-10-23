# grand

`grand`模块实现了对随机数操作的封装和改进，提供了极高的随机数生成性能，并保证每一次调用随机方法时生成的都是不同的随机数值。

使用方式：
```go
import "gitee.com/johng/gf/g/util/grand"
```

方法列表： [godoc.org/github.com/johng-cn/gf/g/util/grand](https://godoc.org/github.com/johng-cn/gf/g/util/grand)
```go
func Rand(min, max int) int
func RandDigits(n int) string
func RandLetters(n int) string
func RandStr(n int) string
func Seed(seed ...int64)
```

基准测试：
```html
goos: linux
goarch: amd64
pkg: gitee.com/johng/gf/g/util/grand
Benchmark_Rand-4   	20000000	        68.1 ns/op
PASS
```