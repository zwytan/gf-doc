# grand

`grand`模块实现了对随机数操作的封装和改进，提供了极高的随机数生成性能，并保证每一次调用随机方法时生成的都是不同的随机数值。

**使用方式**：
```go
import "github.com/gogf/gf/g/util/grand"
```

**接口文档**： [godoc.org/github.com/gogf/gf/g/util/grand](https://godoc.org/github.com/gogf/gf/g/util/grand)
```go
func Meet(num, total int) bool
func MeetProb(prob float32) bool
func Digits(n int) string
func Letters(n int) string
func N(min, max int) int
func Rand(min, max int) int
func RandDigits(n int) string
func RandLetters(n int) string
func RandStr(n int) string
func Str(n int) string
```

基准测试：
```html
goos: linux
goarch: amd64
pkg: github.com/gogf/gf/g/util/grand
Benchmark_Rand-4   	20000000	        68.1 ns/op
PASS
```