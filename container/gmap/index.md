[TOC]

# gmap

并发安全Map，最常用的并发安全数据结构。

**使用场景**：

需要并发安全支持的map数据类型场景。例如，map对象会被多个goroutine读写时。

**使用方式**：
```go
import "github.com/gogf/gf/g/container/gmap"
```

**接口文档**：

[godoc.org/github.com/gogf/gf/g/container/gmap](https://godoc.org/github.com/gogf/gf/g/container/gmap)



## 使用示例
```go
package main

import (
    "fmt"
    "github.com/gogf/gf/g/container/gmap"
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

```shell
CPU: Intel(R) Core(TM) i5-4460  CPU @ 3.20GHz
MEM: 8GB
SYS: Ubuntu 16.04 amd64
```

### 并发安全
```shell
n@john-B85M:~/Workspace/Go/GOPATH/src/github.com/gogf/gf/g/container/gmap$ go test *.go -bench=".*" -benchmem
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
john@john-B85M:~/Workspace/Go/GOPATH/src/github.com/gogf/gf/g/container/gmap$ go test *.go -bench=".*" -benchmem
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
john@john-B85M:~/Workspace/Go/GOPATH/src/github.com/gogf/gf/g/container/gmap$ go test *.go -bench=".*" -benchmem
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
