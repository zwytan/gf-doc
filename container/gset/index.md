# gset

并发安全集合。集合，即不可重复的一组元素，元素项可以为任意类型。

**使用场景**：

并发安全场景下的集合操作。

**使用方式**：
```go
import "github.com/gogf/gf/g/container/gset"
```

**接口文档**：
https://godoc.org/github.com/gogf/gf/g/container/gset


## 使用示例
```go
package main

import (
    "github.com/gogf/gf/g/container/gset"
    "fmt"
)

func main() {
    // 创建一个非并发安全的集合对象
    s := gset.New(false)

    // 添加数据项
    s.Add(1)

    // 批量添加数据项
    s.BatchAdd([]interface{}{1, 2, 3})

    // 集合数据项大小
    fmt.Println(s.Size())

    // 集合中是否存在指定数据项
    fmt.Println(s.Contains(2))

    // 返回数据项slice
    fmt.Println(s.Slice())

    // 删除数据项
    s.Remove(3)

    // 遍历数据项
    s.Iterator(func(v interface{}) bool {
        fmt.Println("Iterator:", v)
        return true
    })

    // 将集合转换为字符串
    fmt.Println(s.String())

    // 并发安全写锁操作
    s.LockFunc(func(m map[interface{}]struct{}) {
        m[4] = struct{}{}
    })

    // 并发安全读锁操作
    s.RLockFunc(func(m map[interface{}]struct{}) {
        fmt.Println(m)
    })

    // 清空集合
    s.Clear()
    fmt.Println(s.Size())
}
```
执行后，输出结果为：
```html
3
true
[1 2 3]
Iterator: 1
Iterator: 2
[1 2]
map[1:{} 2:{} 4:{}]
0
```
