>[danger] # gtype

并发安全的基本类型。

使用方式：
```go
import "gitee.com/johng/gf/g/container/gtype"
```

方法列表（[API文档](https://godoc.org/github.com/johng-cn/gf/g/container/gtype)）：

```go
type Bool
    func NewBool(value ...bool) *Bool
    func (t *Bool) Set(value bool)
    func (t *Bool) Val() bool
type Byte
    func NewByte(value ...byte) *Byte
    func (t *Byte) Add(delta int) byte
    func (t *Byte) Set(value byte)
    func (t *Byte) Val() byte
type Bytes
    func NewBytes(value ...[]byte) *Bytes
    func (t *Bytes) LockFunc(f func(value []byte) []byte)
    func (t *Bytes) RLockFunc(f func(value []byte))
    func (t *Bytes) Set(value []byte)
    func (t *Bytes) Val() []byte
type Float32
    func NewFloat32(value ...float32) *Float32
    func (t *Float32) Add(delta float32) int
    func (t *Float32) Set(value float32)
    func (t *Float32) Val() int
type Float64
    func NewFloat64(value ...float32) *Float64
    func (t *Float64) Add(delta float32) int
    func (t *Float64) Set(value float32)
    func (t *Float64) Val() int
type Int
    func NewInt(value ...int) *Int
    func (t *Int) Add(delta int) int
    func (t *Int) Set(value int)
    func (t *Int) Val() int
type Int32
    func NewInt32(value ...int32) *Int32
    func (t *Int32) Add(delta int32) int32
    func (t *Int32) Set(value int32)
    func (t *Int32) Val() int32
type Int64
    func NewInt64(value ...int64) *Int64
    func (t *Int64) Add(delta int64) int64
    func (t *Int64) Set(value int64)
    func (t *Int64) Val() int64
type Interface
    func NewInterface(value ...interface{}) *Interface
    func (t *Interface) LockFunc(f func(value interface{}) interface{})
    func (t *Interface) RLockFunc(f func(value interface{}))
    func (t *Interface) Set(value interface{})
    func (t *Interface) Val() interface{}
type String
    func NewString(value ...string) *String
    func (t *String) LockFunc(f func(value string) string)
    func (t *String) RLockFunc(f func(value string))
    func (t *String) Set(value string)
    func (t *String) Val() string
type Uint
    func NewUint(value ...uint) *Uint
    func (t *Uint) Add(delta uint) int
    func (t *Uint) Set(value uint)
    func (t *Uint) Val() uint
type Uint32
    func NewUint32(value ...uint32) *Uint32
    func (t *Uint32) Add(delta uint32) uint32
    func (t *Uint32) Set(value uint32)
    func (t *Uint32) Val() uint32
type Uint64
    func NewUint64(value ...uint64) *Uint64
    func (t *Uint64) Add(delta uint64) uint64
    func (t *Uint64) Set(value uint64)
    func (t *Uint64) Val() uint64
```
基准测试结果如下:
```html
john@johnstation:~/Workspace/Go/GOPATH/src/gitee.com/johng/gf/g/container/gtype$ go test *.go -bench=".*"
goos: linux
goarch: amd64
BenchmarkInt_Set-8         	300000000	         5.65 ns/op
BenchmarkInt_Val-8         	2000000000	         0.35 ns/op
BenchmarkInt_Add-8         	300000000	         5.68 ns/op
BenchmarkInt32_Set-8       	300000000	         5.67 ns/op
BenchmarkInt32_Val-8       	2000000000	         0.36 ns/op
BenchmarkInt32_Add-8       	300000000	         5.68 ns/op
BenchmarkInt64_Set-8       	300000000	         5.66 ns/op
BenchmarkInt64_Val-8       	2000000000	         0.35 ns/op
BenchmarkInt64_Add-8       	300000000	         5.67 ns/op
BenchmarkUint_Set-8        	300000000	         5.68 ns/op
BenchmarkUint_Val-8        	2000000000	         0.34 ns/op
BenchmarkUint_Add-8        	300000000	         5.68 ns/op
BenchmarkUint32_Set-8      	300000000	         5.65 ns/op
BenchmarkUint32_Val-8      	2000000000	         0.34 ns/op
BenchmarkUint32_Add-8      	300000000	         5.66 ns/op
BenchmarkUint64_Set-8      	300000000	         5.68 ns/op
BenchmarkUint64_Val-8      	2000000000	         0.34 ns/op
BenchmarkUint64_Add-8      	300000000	         5.68 ns/op
BenchmarkBool_Set-8        	300000000	         5.67 ns/op
BenchmarkBool_Val-8        	2000000000	         0.34 ns/op
BenchmarkString_Set-8      	30000000	        48.5 ns/op
BenchmarkString_Val-8      	100000000	        12.3 ns/op
BenchmarkBytes_Set-8       	50000000	        34.9 ns/op
BenchmarkBytes_Val-8       	100000000	        12.5 ns/op
BenchmarkInterface_Set-8   	50000000	        34.2 ns/op
BenchmarkInterface_Val-8   	100000000	        12.2 ns/op
PASS
ok  	command-line-arguments	43.447s
```




