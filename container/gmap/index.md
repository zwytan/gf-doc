[TOC]

# gmap

支持可选并发安全特性的`map`容器，最常用的并发安全数据结构。

该模块包含多个数据结构的`map`容器：`Map/HashMap`、`TreeMap`和`ListMap`。

|类型|数据结构|平均复杂度|支持排序|有序遍历|说明
|---|---|---|---|---|---
|`Map/HashMap`|哈希表|O(1)|否|否|高性能读写操作，内存占用较高，随机遍历
|`ListMap`|哈希表+双向链表|O(2)|否|是|支持按照写入顺序遍历，内存占用较高
|`TreeMap`|红黑树|O(log N)|是|是|内存占用紧凑，支持键名排序及有序遍历

> 其中`Map`为`HashMap`的别名，为方便开发调用，`gmap`模块支持多种以哈希表为基础数据结构的常见类型`map`定义：`IntIntMap`、`IntStrMap`、`IntAnyMap`、`StrIntMap`、`StrStrMap`、`StrAnyMap`。

> 参考链接：https://en.wikipedia.org/wiki/Hash_table

**使用场景**：

任何`map`/哈希表/关联数组使用场景。

**使用方式**：
```go
import "github.com/gogf/gf/g/container/gmap"
```

**接口文档**：

https://godoc.org/github.com/gogf/gf/g/container/gmap



## 使用示例

### 示例1，基本使用

```go
package main

import (
    "fmt"
    "github.com/gogf/gf/g/container/gmap"
)

func main() {
    // 创建一个默认的gmap对象，
    // 默认情况下该gmap对象支持并发安全特性，
    // 初始化时可以给定true参数关闭并发安全特性，当做一个普通的map使用。
    m := gmap.New()

    // 设置键值对
    for i := 0; i < 10; i++ {
        m.Set(i, i)
    }
    // 查询大小
    fmt.Println(m.Size())
    // 批量设置键值对(不同的数据类型对象参数不同)
    m.Sets(map[interface{}]interface{}{
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
    m.Removes([]interface{}{10, 11})
    fmt.Println(m.Size())

    // 当前键名列表(随机排序)
    fmt.Println(m.Keys())
    // 当前键值列表(随机排序)
    fmt.Println(m.Values())

    // 查询键名，当键值不存在时，写入给定的默认值
    fmt.Println(m.GetOrSet(100, 100))

    // 删除键值对，并返回对应的键值
    fmt.Println(m.Remove(100))

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

### 示例2，有序遍历

我们来看一下三种不同类型`map`的有序性遍历示例。

```go
package main

import (
    "fmt"
    "github.com/gogf/gf/g"
    "github.com/gogf/gf/g/container/gmap"
    "github.com/gogf/gf/g/util/gutil"
)

func main() {
    array   := g.Slice{2, 3, 1, 5, 4, 6, 8, 7, 9}
    hashMap := gmap.New(true)
    listMap := gmap.NewListMap(true)
    treeMap := gmap.NewTreeMap(gutil.ComparatorInt, true)
    for _, v := range array {
        hashMap.Set(v, v)
    }
    for _, v := range array {
        listMap.Set(v, v)
    }
    for _, v := range array {
        treeMap.Set(v, v)
    }
    fmt.Println("HashMap   Keys:", hashMap.Keys())
    fmt.Println("HashMap Values:", hashMap.Values())
    fmt.Println("ListMap   Keys:", listMap.Keys())
    fmt.Println("ListMap Values:", listMap.Values())
    fmt.Println("TreeMap   Keys:", treeMap.Keys())
    fmt.Println("TreeMap Values:", treeMap.Values())
}
```
执行后，输出结果为：
```html
HashMap   Keys: [4 6 8 7 9 2 3 1 5]
HashMap Values: [6 8 4 3 1 5 7 9 2]
ListMap   Keys: [2 3 1 5 4 6 8 7 9]
ListMap Values: [2 3 1 5 4 6 8 7 9]
TreeMap   Keys: [1 2 3 4 5 6 7 8 9]
TreeMap Values: [1 2 3 4 5 6 7 8 9]
```

## 并发安全

`gmap`支持并发安全选项开关，在默认情况下是`并发安全`的，但是在某些对性能要求比较高的场景下，用户可以选择主动关闭`gmap`的并发安全特性(传递初始化开关参数`unsafe`值为`true`, 必须在初始化时设定，不能运行时动态设定)，性能会得到一定提升，如：
```go
m := gmap.New(true)
```

不仅仅是`gmap`模块，`gf`框架的其他并发安全数据结构也支持并发安全特性开关，来提升性能或者简化原本复杂的数据结构操作。


## 性能测试

### 并发安全

https://github.com/gogf/gf/blob/master/g/container/gmap/gmap_z_bench_safe_test.go

```shell
goos: darwin
goarch: amd64
Benchmark_IntIntMap_Set-4               10000000               202 ns/op              15 B/op          0 allocs/op
Benchmark_IntAnyMap_Set-4               10000000               262 ns/op              29 B/op          1 allocs/op
Benchmark_IntStrMap_Set-4               10000000               241 ns/op              22 B/op          0 allocs/op
Benchmark_AnyAnyMap_Set-4                5000000               359 ns/op              40 B/op          2 allocs/op
Benchmark_StrIntMap_Set-4                5000000               305 ns/op              26 B/op          1 allocs/op
Benchmark_StrAnyMap_Set-4                5000000               354 ns/op              40 B/op          2 allocs/op
Benchmark_StrStrMap_Set-4                5000000               338 ns/op              32 B/op          1 allocs/op
Benchmark_IntIntMap_Get-4               20000000              86.6 ns/op               0 B/op          0 allocs/op
Benchmark_IntAnyMap_Get-4               30000000              69.7 ns/op               0 B/op          0 allocs/op
Benchmark_IntStrMap_Get-4               30000000              69.6 ns/op               0 B/op          0 allocs/op
Benchmark_AnyAnyMap_Get-4               20000000              74.4 ns/op               0 B/op          0 allocs/op
Benchmark_StrIntMap_Get-4               20000000               116 ns/op               7 B/op          0 allocs/op
Benchmark_StrAnyMap_Get-4               20000000              92.3 ns/op               7 B/op          0 allocs/op
Benchmark_StrStrMap_Get-4               20000000              91.9 ns/op               7 B/op          0 allocs/op
```
### 非并发安全

https://github.com/gogf/gf/blob/master/g/container/gmap/gmap_z_bench_unsafe_test.go

```shell
goos: darwin
goarch: amd64
Benchmark_Unsafe_IntIntMap_Set-4        10000000               318 ns/op              62 B/op          0 allocs/op
Benchmark_Unsafe_IntAnyMap_Set-4         5000000               282 ns/op              57 B/op          1 allocs/op
Benchmark_Unsafe_IntStrMap_Set-4         5000000               332 ns/op              82 B/op          1 allocs/op
Benchmark_Unsafe_AnyAnyMap_Set-4         3000000               471 ns/op              73 B/op          2 allocs/op
Benchmark_Unsafe_StrIntMap_Set-4         5000000               429 ns/op              82 B/op          1 allocs/op
Benchmark_Unsafe_StrAnyMap_Set-4         3000000               424 ns/op              73 B/op          2 allocs/op
Benchmark_Unsafe_StrStrMap_Set-4         2000000               515 ns/op              96 B/op          2 allocs/op
Benchmark_Unsafe_IntIntMap_Get-4        10000000               133 ns/op               0 B/op          0 allocs/op
Benchmark_Unsafe_IntAnyMap_Get-4        20000000               134 ns/op               0 B/op          0 allocs/op
Benchmark_Unsafe_IntStrMap_Get-4        10000000               126 ns/op               0 B/op          0 allocs/op
Benchmark_Unsafe_AnyAnyMap_Get-4        10000000               166 ns/op               0 B/op          0 allocs/op
Benchmark_Unsafe_StrIntMap_Get-4         5000000               246 ns/op               7 B/op          0 allocs/op
Benchmark_Unsafe_StrAnyMap_Get-4        10000000               238 ns/op               7 B/op          0 allocs/op
Benchmark_Unsafe_StrStrMap_Get-4         5000000               229 ns/op               7 B/op          0 allocs/op
```

### 不同类型map性能

https://github.com/gogf/gf/blob/master/g/container/gmap/gmap_z_bench_maps_test.go

```shell
goos: darwin
goarch: amd64
Benchmark_HashMap_Set-4                  5000000               349 ns/op              40 B/op          2 allocs/op
Benchmark_ListMap_Set-4                  3000000               455 ns/op              87 B/op          3 allocs/op
Benchmark_TreeMap_Set-4                  3000000               481 ns/op              28 B/op          2 allocs/op
Benchmark_HashMap_Get-4                 30000000              67.8 ns/op               0 B/op          0 allocs/op
Benchmark_ListMap_Get-4                 20000000              74.5 ns/op               0 B/op          0 allocs/op
Benchmark_TreeMap_Get-4                 20000000               189 ns/op               8 B/op          1 allocs/op
```


### gmap与sync.Map性能比较

go语言从`1.9`版本开始引入了并发安全的`sync.Map`，但`gmap`比较于标准库的`sync.Map`性能更加优异，并且功能更加丰富。 

我们来看看基准测试对比结果：https://github.com/gogf/gf/blob/master/g/container/gmap/gmap_z_bench_syncmap_test.go
```shell
goos: darwin
goarch: amd64
Benchmark_GMapSet-4                     10000000               209 ns/op              15 B/op          0 allocs/op
Benchmark_SyncMapSet-4                   3000000               451 ns/op              67 B/op          3 allocs/op
Benchmark_GMapGet-4                     30000000              66.4 ns/op               0 B/op          0 allocs/op
Benchmark_SyncMapGet-4                  30000000              36.0 ns/op               0 B/op          0 allocs/op
Benchmark_GMapRemove-4                  10000000               207 ns/op               0 B/op          0 allocs/op
Benchmark_SyncMapRmove-4                30000000              42.4 ns/op               0 B/op          0 allocs/op
```

