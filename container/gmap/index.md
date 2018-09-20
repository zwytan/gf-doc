[TOC]

# gmap

并发安全Map，最常用的并发安全数据结构。

**使用场景**：

需要并发安全支持的map数据类型场景。例如，map对象会被多个goroutine读写时。

**使用方式**：
```go
import "gitee.com/johng/gf/g/container/gmap"
```

**方法列表**：[godoc.org/github.com/johng-cn/gf/g/container/gmap](https://godoc.org/github.com/johng-cn/gf/g/container/gmap)

<!-- 由于`gmap`包下的对象及方法比较多，这里便不一一列举。`gmap`下包含了多种数据类型的map，可以使用 `gmap.New*` 方法来创建。 -->

```go
type IntBoolMap
    func NewIntBoolMap(safe ...bool) *IntBoolMap
    func (this *IntBoolMap) BatchRemove(keys []int)
    func (this *IntBoolMap) BatchSet(m map[int]bool)
    func (this *IntBoolMap) Clear()
    func (this *IntBoolMap) Clone() map[int]bool
    func (this *IntBoolMap) Contains(key int) bool
    func (this *IntBoolMap) Get(key int) bool
    func (this *IntBoolMap) GetOrSet(key int, value bool) bool
    func (this *IntBoolMap) GetOrSetFunc(key int, f func() bool) bool
    func (this *IntBoolMap) GetOrSetFuncLock(key int, f func() bool) bool
    func (this *IntBoolMap) IsEmpty() bool
    func (this *IntBoolMap) Iterator(f func(k int, v bool) bool)
    func (this *IntBoolMap) Keys() []int
    func (this *IntBoolMap) LockFunc(f func(m map[int]bool))
    func (this *IntBoolMap) RLockFunc(f func(m map[int]bool))
    func (this *IntBoolMap) Remove(key int) bool
    func (this *IntBoolMap) Set(key int, val bool)
    func (this *IntBoolMap) SetIfNotExist(key int, value bool) bool
    func (this *IntBoolMap) Size() int
type IntIntMap
    func NewIntIntMap(safe ...bool) *IntIntMap
    func (this *IntIntMap) BatchRemove(keys []int)
    func (this *IntIntMap) BatchSet(m map[int]int)
    func (this *IntIntMap) Clear()
    func (this *IntIntMap) Clone() map[int]int
    func (this *IntIntMap) Contains(key int) bool
    func (this *IntIntMap) Get(key int) int
    func (this *IntIntMap) GetOrSet(key int, value int) int
    func (this *IntIntMap) GetOrSetFunc(key int, f func() int) int
    func (this *IntIntMap) GetOrSetFuncLock(key int, f func() int) int
    func (this *IntIntMap) IsEmpty() bool
    func (this *IntIntMap) Iterator(f func(k int, v int) bool)
    func (this *IntIntMap) Keys() []int
    func (this *IntIntMap) LockFunc(f func(m map[int]int))
    func (this *IntIntMap) RLockFunc(f func(m map[int]int))
    func (this *IntIntMap) Remove(key int) int
    func (this *IntIntMap) Set(key int, val int)
    func (this *IntIntMap) SetIfNotExist(key int, value int) bool
    func (this *IntIntMap) Size() int
    func (this *IntIntMap) Values() []int
type IntInterfaceMap
    func NewIntInterfaceMap(safe ...bool) *IntInterfaceMap
    func (this *IntInterfaceMap) BatchRemove(keys []int)
    func (this *IntInterfaceMap) BatchSet(m map[int]interface{})
    func (this *IntInterfaceMap) Clear()
    func (this *IntInterfaceMap) Clone() map[int]interface{}
    func (this *IntInterfaceMap) Contains(key int) bool
    func (this *IntInterfaceMap) Get(key int) interface{}
    func (this *IntInterfaceMap) GetOrSet(key int, value interface{}) interface{}
    func (this *IntInterfaceMap) GetOrSetFunc(key int, f func() interface{}) interface{}
    func (this *IntInterfaceMap) GetOrSetFuncLock(key int, f func() interface{}) interface{}
    func (this *IntInterfaceMap) IsEmpty() bool
    func (this *IntInterfaceMap) Iterator(f func(k int, v interface{}) bool)
    func (this *IntInterfaceMap) Keys() []int
    func (this *IntInterfaceMap) LockFunc(f func(m map[int]interface{}))
    func (this *IntInterfaceMap) RLockFunc(f func(m map[int]interface{}))
    func (this *IntInterfaceMap) Remove(key int) interface{}
    func (this *IntInterfaceMap) Set(key int, val interface{})
    func (this *IntInterfaceMap) SetIfNotExist(key int, value interface{}) bool
    func (this *IntInterfaceMap) Size() int
    func (this *IntInterfaceMap) Values() []interface{}
type IntStringMap
    func NewIntStringMap(safe ...bool) *IntStringMap
    func (this *IntStringMap) BatchRemove(keys []int)
    func (this *IntStringMap) BatchSet(m map[int]string)
    func (this *IntStringMap) Clear()
    func (this *IntStringMap) Clone() map[int]string
    func (this *IntStringMap) Contains(key int) bool
    func (this *IntStringMap) Get(key int) string
    func (this *IntStringMap) GetOrSet(key int, value string) string
    func (this *IntStringMap) GetOrSetFunc(key int, f func() string) string
    func (this *IntStringMap) GetOrSetFuncLock(key int, f func() string) string
    func (this *IntStringMap) IsEmpty() bool
    func (this *IntStringMap) Iterator(f func(k int, v string) bool)
    func (this *IntStringMap) Keys() []int
    func (this *IntStringMap) LockFunc(f func(m map[int]string))
    func (this *IntStringMap) RLockFunc(f func(m map[int]string))
    func (this *IntStringMap) Remove(key int) string
    func (this *IntStringMap) Set(key int, val string)
    func (this *IntStringMap) SetIfNotExist(key int, value string) bool
    func (this *IntStringMap) Size() int
    func (this *IntStringMap) Values() []string
type InterfaceInterfaceMap
    func NewInterfaceInterfaceMap(safe ...bool) *InterfaceInterfaceMap
    func (this *InterfaceInterfaceMap) BatchRemove(keys []interface{})
    func (this *InterfaceInterfaceMap) BatchSet(m map[interface{}]interface{})
    func (this *InterfaceInterfaceMap) Clear()
    func (this *InterfaceInterfaceMap) Clone() map[interface{}]interface{}
    func (this *InterfaceInterfaceMap) Contains(key interface{}) bool
    func (this *InterfaceInterfaceMap) Get(key interface{}) interface{}
    func (this *InterfaceInterfaceMap) GetOrSet(key interface{}, value interface{}) interface{}
    func (this *InterfaceInterfaceMap) GetOrSetFunc(key interface{}, f func() interface{}) interface{}
    func (this *InterfaceInterfaceMap) GetOrSetFuncLock(key interface{}, f func() interface{}) interface{}
    func (this *InterfaceInterfaceMap) IsEmpty() bool
    func (this *InterfaceInterfaceMap) Iterator(f func(k interface{}, v interface{}) bool)
    func (this *InterfaceInterfaceMap) Keys() []interface{}
    func (this *InterfaceInterfaceMap) LockFunc(f func(m map[interface{}]interface{}))
    func (this *InterfaceInterfaceMap) RLockFunc(f func(m map[interface{}]interface{}))
    func (this *InterfaceInterfaceMap) Remove(key interface{}) interface{}
    func (this *InterfaceInterfaceMap) Set(key interface{}, val interface{})
    func (this *InterfaceInterfaceMap) SetIfNotExist(key interface{}, value interface{}) bool
    func (this *InterfaceInterfaceMap) Size() int
    func (this *InterfaceInterfaceMap) Values() []interface{}
type Map
    func New(safe ...bool) *Map
type StringBoolMap
    func NewStringBoolMap(safe ...bool) *StringBoolMap
    func (this *StringBoolMap) BatchRemove(keys []string)
    func (this *StringBoolMap) BatchSet(m map[string]bool)
    func (this *StringBoolMap) Clear()
    func (this *StringBoolMap) Clone() map[string]bool
    func (this *StringBoolMap) Contains(key string) bool
    func (this *StringBoolMap) Get(key string) bool
    func (this *StringBoolMap) GetOrSet(key string, value bool) bool
    func (this *StringBoolMap) GetOrSetFunc(key string, f func() bool) bool
    func (this *StringBoolMap) GetOrSetFuncLock(key string, f func() bool) bool
    func (this *StringBoolMap) IsEmpty() bool
    func (this *StringBoolMap) Iterator(f func(k string, v bool) bool)
    func (this *StringBoolMap) Keys() []string
    func (this *StringBoolMap) LockFunc(f func(m map[string]bool))
    func (this *StringBoolMap) RLockFunc(f func(m map[string]bool))
    func (this *StringBoolMap) Remove(key string) bool
    func (this *StringBoolMap) Set(key string, val bool)
    func (this *StringBoolMap) SetIfNotExist(key string, value bool) bool
    func (this *StringBoolMap) Size() int
type StringIntMap
    func NewStringIntMap(safe ...bool) *StringIntMap
    func (this *StringIntMap) BatchRemove(keys []string)
    func (this *StringIntMap) BatchSet(m map[string]int)
    func (this *StringIntMap) Clear()
    func (this *StringIntMap) Clone() map[string]int
    func (this *StringIntMap) Contains(key string) bool
    func (this *StringIntMap) Get(key string) int
    func (this *StringIntMap) GetOrSet(key string, value int) int
    func (this *StringIntMap) GetOrSetFunc(key string, f func() int) int
    func (this *StringIntMap) GetOrSetFuncLock(key string, f func() int) int
    func (this *StringIntMap) IsEmpty() bool
    func (this *StringIntMap) Iterator(f func(k string, v int) bool)
    func (this *StringIntMap) Keys() []string
    func (this *StringIntMap) LockFunc(f func(m map[string]int))
    func (this *StringIntMap) RLockFunc(f func(m map[string]int))
    func (this *StringIntMap) Remove(key string) int
    func (this *StringIntMap) Set(key string, val int)
    func (this *StringIntMap) SetIfNotExist(key string, value int) bool
    func (this *StringIntMap) Size() int
    func (this *StringIntMap) Values() []int
type StringInterfaceMap
    func NewStringInterfaceMap(safe ...bool) *StringInterfaceMap
    func (this *StringInterfaceMap) BatchRemove(keys []string)
    func (this *StringInterfaceMap) BatchSet(m map[string]interface{})
    func (this *StringInterfaceMap) Clear()
    func (this *StringInterfaceMap) Clone() map[string]interface{}
    func (this *StringInterfaceMap) Contains(key string) bool
    func (this *StringInterfaceMap) Get(key string) interface{}
    func (this *StringInterfaceMap) GetOrSet(key string, value interface{}) interface{}
    func (this *StringInterfaceMap) GetOrSetFunc(key string, f func() interface{}) interface{}
    func (this *StringInterfaceMap) GetOrSetFuncLock(key string, f func() interface{}) interface{}
    func (this *StringInterfaceMap) IsEmpty() bool
    func (this *StringInterfaceMap) Iterator(f func(k string, v interface{}) bool)
    func (this *StringInterfaceMap) Keys() []string
    func (this *StringInterfaceMap) LockFunc(f func(m map[string]interface{}))
    func (this *StringInterfaceMap) RLockFunc(f func(m map[string]interface{}))
    func (this *StringInterfaceMap) Remove(key string) interface{}
    func (this *StringInterfaceMap) Set(key string, val interface{})
    func (this *StringInterfaceMap) SetIfNotExist(key string, value interface{}) bool
    func (this *StringInterfaceMap) Size() int
    func (this *StringInterfaceMap) Values() []interface{}
type StringStringMap
    func NewStringStringMap(safe ...bool) *StringStringMap
    func (this *StringStringMap) BatchRemove(keys []string)
    func (this *StringStringMap) BatchSet(m map[string]string)
    func (this *StringStringMap) Clear()
    func (this *StringStringMap) Clone() map[string]string
    func (this *StringStringMap) Contains(key string) bool
    func (this *StringStringMap) Get(key string) string
    func (this *StringStringMap) GetOrSet(key string, value string) string
    func (this *StringStringMap) GetOrSetFunc(key string, f func() string) string
    func (this *StringStringMap) GetOrSetFuncLock(key string, f func() string) string
    func (this *StringStringMap) IsEmpty() bool
    func (this *StringStringMap) Iterator(f func(k string, v string) bool)
    func (this *StringStringMap) Keys() []string
    func (this *StringStringMap) LockFunc(f func(m map[string]string))
    func (this *StringStringMap) RLockFunc(f func(m map[string]string))
    func (this *StringStringMap) Remove(key string) string
    func (this *StringStringMap) Set(key string, val string)
    func (this *StringStringMap) SetIfNotExist(key string, value string) bool
    func (this *StringStringMap) Size() int
    func (this *StringStringMap) Values() []string
```


## 使用示例
```go
package main

import (
    "fmt"
    "gitee.com/johng/gf/g/container/gmap"
)

func main() {
    // 创建一个默认的gmap对象，
    // 默认情况下该gmap对象支持并发安全特性，
    // 初始化时可以给定false参数关闭并发安全特性，当做一个普通的map使用。
    m := gmap.New()

    // 设置键值对
    for i := 0; i < 10; i++ {
        m.Set(i, i)
    }
    // 查询大小
    fmt.Println(m.Size())
    // 批量设置键值对(不同的数据类型对象参数不同)
    m.BatchSet(map[interface{}]interface{}{
        10 : 10,
        11 : 11,
    })
    fmt.Println(m.Size())

    // 查询是否存在
    fmt.Println(m.Contains(1))

    // 查询键值
    fmt.Println(m.Get(1))

    // 删除数据项
    m.Remove(9)
    fmt.Println(m.Size())

    // 批量删除
    m.BatchRemove([]interface{}{10, 11})
    fmt.Println(m.Size())

    // 当前键名列表(随机排序)
    fmt.Println(m.Keys())
    // 当前键值列表(随机排序)
    fmt.Println(m.Values())

    // 查询键名，当键值不存在时，写入给定的默认值
    fmt.Println(m.GetWithDefault(100, 100))

    // 删除键值对，并返回对应的键值
    fmt.Println(m.GetAndRemove(100))

    // 遍历map
    m.Iterator(func(k interface{}, v interface{}) bool {
        fmt.Printf("%v:%v ", k, v)
        return true
    })

    // 自定义写锁操作
    m.LockFunc(func(m map[interface{}]interface{}) {
        m[99] = 99
    })

    // 自定义读锁操作
    m.RLockFunc(func(m map[interface{}]interface{}) {
        fmt.Println(m[99])
    })

    // 清空map
    m.Clear()

    // 判断map是否为空
    fmt.Println(m.IsEmpty())
}
```

执行后，输出结果为：

```html
10
12
true
1
11
9
[0 1 2 4 6 7 3 5 8]
[3 5 8 0 1 2 4 6 7]
100
100
3:3 5:5 8:8 7:7 0:0 1:1 2:2 4:4 6:6 99
true
```

## 并发安全

`gmap`在默认情况下是`并发安全`的，但是在某些对性能要求比较高的场景下，又或者只是想使用`gmap`对象来便于操作`map`，那么用户可以选择主动关闭`gmap`的并发安全特性(必须在初始化时设定，不能运行时动态设定)，性能会得到一定提升。关闭并发安全特性可以在创建`gmap`对象时传递`false`参数，如：
```go
m := gmap.New(false)
```

此外，即使在未开启并发安全特性的情况下，也可以使用`Lock`/`RLock`方法来并发安全地自定义操作`gmap`对象。


不仅仅是`gmap`，gf框架的其他并发安全数据结构也可以关闭并发安全特性，来提升性能或者简化原本复杂的数据结构操作。


## 性能测试

### 测试环境

* CPU: Intel(R) Core(TM) i5-4460  CPU @ 3.20GHz
* MEM: 8GB
* SYS: Ubuntu 16.04 amd64

### 并发安全
```shell
n@john-B85M:~/Workspace/Go/GOPATH/src/gitee.com/johng/gf/g/container/gmap$ go test *.go -bench=".*" -benchmem
goos: linux
goarch: amd64
Benchmark_IntBoolMap_Set-4                      10000000        245 ns/op       38 B/op        0 allocs/op
Benchmark_IntIntMap_Set-4                        5000000        283 ns/op       53 B/op        0 allocs/op
Benchmark_IntInterfaceMap_Set-4                  5000000        320 ns/op       82 B/op        1 allocs/op
Benchmark_IntStringMap_Set-4                     5000000        342 ns/op       82 B/op        1 allocs/op
Benchmark_InterfaceInterfaceMap_Set-4            3000000        363 ns/op       73 B/op        2 allocs/op
Benchmark_StringBoolMap_Set-4                    5000000        392 ns/op       62 B/op        1 allocs/op
Benchmark_StringIntMap_Set-4                     5000000        356 ns/op       56 B/op        1 allocs/op
Benchmark_StringInterfaceMap_Set-4               3000000        361 ns/op       73 B/op        2 allocs/op
Benchmark_StringStringMap_Set-4                  3000000        385 ns/op       73 B/op        2 allocs/op
Benchmark_IntBoolMap_Get-4                      20000000        133 ns/op        0 B/op        0 allocs/op
Benchmark_IntIntMap_Get-4                       10000000        132 ns/op        0 B/op        0 allocs/op
Benchmark_IntInterfaceMap_Get-4                 10000000        137 ns/op        0 B/op        0 allocs/op
Benchmark_IntStringMap_Get-4                    10000000        147 ns/op        0 B/op        0 allocs/op
Benchmark_InterfaceInterfaceMap_Get-4           10000000        155 ns/op        0 B/op        0 allocs/op
Benchmark_StringBoolMap_Get-4                   10000000        209 ns/op        7 B/op        0 allocs/op
Benchmark_StringIntMap_Get-4                    10000000        213 ns/op        7 B/op        0 allocs/op
Benchmark_StringInterfaceMap_Get-4              10000000        242 ns/op        7 B/op        0 allocs/op
Benchmark_StringStringMap_Get-4                 10000000        267 ns/op        7 B/op        0 allocs/op
```
### 非并发安全

```shell
john@john-B85M:~/Workspace/Go/GOPATH/src/gitee.com/johng/gf/g/container/gmap$ go test *.go -bench=".*" -benchmem
goos: linux
goarch: amd64
Benchmark_Unsafe_IntBoolMap_Set-4               10000000        211 ns/op       38 B/op        0 allocs/op
Benchmark_Unsafe_IntIntMap_Set-4                10000000        236 ns/op       62 B/op        0 allocs/op
Benchmark_Unsafe_IntInterfaceMap_Set-4           5000000        279 ns/op       82 B/op        1 allocs/op
Benchmark_Unsafe_IntStringMap_Set-4              5000000        304 ns/op       82 B/op        1 allocs/op
Benchmark_Unsafe_InterfaceInterfaceMap_Set-4    10000000        488 ns/op      112 B/op        2 allocs/op
Benchmark_Unsafe_StringBoolMap_Set-4             5000000        366 ns/op       62 B/op        1 allocs/op
Benchmark_Unsafe_StringIntMap_Set-4              5000000        378 ns/op       56 B/op        1 allocs/op
Benchmark_Unsafe_StringInterfaceMap_Set-4        3000000        472 ns/op       73 B/op        2 allocs/op
Benchmark_Unsafe_StringStringMap_Set-4           2000000        510 ns/op       96 B/op        2 allocs/op
Benchmark_Unsafe_IntBoolMap_Get-4               20000000        110 ns/op        0 B/op        0 allocs/op
Benchmark_Unsafe_IntIntMap_Get-4                20000000        111 ns/op        0 B/op        0 allocs/op
Benchmark_Unsafe_IntInterfaceMap_Get-4          20000000        122 ns/op        0 B/op        0 allocs/op
Benchmark_Unsafe_IntStringMap_Get-4             20000000        118 ns/op        0 B/op        0 allocs/op
Benchmark_Unsafe_InterfaceInterfaceMap_Get-4    10000000        230 ns/op        0 B/op        0 allocs/op
Benchmark_Unsafe_StringBoolMap_Get-4            10000000        200 ns/op        7 B/op        0 allocs/op
Benchmark_Unsafe_StringIntMap_Get-4             10000000        202 ns/op        7 B/op        0 allocs/op
Benchmark_Unsafe_StringInterfaceMap_Get-4       10000000        218 ns/op        7 B/op        0 allocs/op
Benchmark_Unsafe_StringStringMap_Get-4          10000000        200 ns/op        7 B/op        0 allocs/op
```


### gmap与sync.Map

go语言从`1.9`版本开始引入了并发安全的`sync.Map`，我们来看看基准测试结果：
```shell
john@john-B85M:~/Workspace/Go/GOPATH/src/gitee.com/johng/gf/g/container/gmap$ go test *.go -bench=".*" -benchmem
goos: linux
goarch: amd64
BenchmarkGmapSet-4                              10000000        324 ns/op       62 B/op        0 allocs/op
BenchmarkSyncmapSet-4                            2000000        697 ns/op      105 B/op        4 allocs/op
BenchmarkGmapGet-4                              10000000        149 ns/op        0 B/op        0 allocs/op
BenchmarkSyncmapGet-4                           10000000        154 ns/op        0 B/op        0 allocs/op
BenchmarkGmapRemove-4                           10000000        126 ns/op        0 B/op        0 allocs/op
BenchmarkSyncmapRmove-4                         10000000        118 ns/op        0 B/op        0 allocs/op
```

可以看到，在写入/删除这块`gmap`的性能与标准库的`sync.Map`对象没有差别，但在写入这块性能优势比较明显。
