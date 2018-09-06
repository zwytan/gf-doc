[TOC]

# gmap

并发安全Map，最常用的并发安全数据结构。

使用场景：

需要并发安全支持的map数据类型场景。例如，map对象会被多个goroutine读写时。

使用方式：
```go
import "gitee.com/johng/gf/g/container/gmap"
```

方法列表：[godoc.org/github.com/johng-cn/gf/g/container/gmap](https://godoc.org/github.com/johng-cn/gf/g/container/gmap)

由于`gmap`包下的对象及方法比较多，这里便不一一列举。`gmap`下包含了多种数据类型的map，可以使用 `gmap.New*` 方法来创建。


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

`gmap`在默认情况下是`并发安全`的，但是在某些对性能要求比较高的场景下，又或者只是想使用`gmap`对象来便于操作`map`，那么用户选择可以主动关闭`gmap`的并发安全特性(必须在初始化时设定，不能运行时动态设定)，性能会得到一定提升。关闭并发安全特性可以在创建`gmap`对象时传递`false`参数，如：
```go
m := gmap.New(false)
```

不仅仅是`gmap`，gf框架的其他并发安全数据结构也可以关闭并发安全特性，来提升性能或者简化原本复杂的数据结构操作。


## gmap与sync.Map

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