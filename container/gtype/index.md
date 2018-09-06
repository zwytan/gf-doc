[TOC]

# gtype

并发安全的基本类型。

使用场景：

`gtype`使用得非常频繁，任何需要并发安全的场景下都适用。
在普通的并发安全场景中，一个基本类型的变量，特别是一个struct的属性，往往使用一把(读写)锁或者多把(读写)锁来进行管理。
但是在这样的使用中，变量/struct/属性的操作性能**十分低下**，且由于锁机制的存在往往使得操作变得相当复杂，必须小心翼翼地维护好变量/属性的并发安全性(特别是使用到的`(RW)Mutex`)。

`gtype`针对于最常用的基本数据类型，提供了对应的并发安全数据类型，便于在并发安全场景下更好地维护变量/属性，开发者无需在struct中再创建和维护繁琐的`(RW)Mutex`。且`gtype`内部绝大多数基本类型都使用了`atomic`原子操作来维护并发安全性，因此效率会比`(RW)Mutex`互斥锁高出数十倍。

使用方式：
```go
import "gitee.com/johng/gf/g/container/gtype"
```
方法列表：[godoc.org/github.com/johng-cn/gf/g/container/gtype](https://godoc.org/github.com/johng-cn/gf/g/container/gtype)

由于`gtype`包下的基本类型比较多，这里便不一一列举。任何并发安全的基本类型，可以使用 `gtype.New*` 方法来创建。


## 性能测试

基准测试结果如下:
```shell
john@johnstation:~/Workspace/Go/GOPATH/src/gitee.com/johng/gf/g/container/gtype$ go test *.go -bench=".*"
goos: linux
goarch: amd64
BenchmarkInt_Set-8          300000000           5.65 ns/op
BenchmarkInt_Val-8          2000000000          0.35 ns/op
BenchmarkInt_Add-8          300000000           5.68 ns/op
BenchmarkInt32_Set-8        300000000           5.67 ns/op
BenchmarkInt32_Val-8        2000000000          0.36 ns/op
BenchmarkInt32_Add-8        300000000           5.68 ns/op
BenchmarkInt64_Set-8        300000000           5.66 ns/op
BenchmarkInt64_Val-8        2000000000          0.35 ns/op
BenchmarkInt64_Add-8        300000000           5.67 ns/op
BenchmarkUint_Set-8         300000000           5.68 ns/op
BenchmarkUint_Val-8         2000000000          0.34 ns/op
BenchmarkUint_Add-8         300000000           5.68 ns/op
BenchmarkUint32_Set-8       300000000           5.65 ns/op
BenchmarkUint32_Val-8       2000000000          0.34 ns/op
BenchmarkUint32_Add-8       300000000           5.66 ns/op
BenchmarkUint64_Set-8       300000000           5.68 ns/op
BenchmarkUint64_Val-8       2000000000          0.34 ns/op
BenchmarkUint64_Add-8       300000000           5.68 ns/op
BenchmarkBool_Set-8         300000000           5.67 ns/op
BenchmarkBool_Val-8         2000000000          0.34 ns/op
BenchmarkString_Set-8       30000000           48.5 ns/op
BenchmarkString_Val-8       100000000          12.3 ns/op
BenchmarkBytes_Set-8        50000000           34.9 ns/op
BenchmarkBytes_Val-8        100000000          12.5 ns/op
BenchmarkInterface_Set-8    50000000           34.2 ns/op
BenchmarkInterface_Val-8    100000000          12.2 ns/op
PASS
ok   command-line-arguments 43.447s
```


## 使用示例

`gtype`并发安全基本类型的使用非常简单，往往就以下几个方法(以`gtype.Int`类型举例)：

```go
// 创建并发安全基本类型对象
func NewInt(value...int) *Int
// 克隆一个对象
func (t *Int) Clone() *Int
// 设置值
func (t *Int) Set(value int)
// 获取值
func (t *Int) Val() int
// (整型/浮点型有效)数值 增加/删除 delta
func (t *Int) Add(delta int) int {
```
