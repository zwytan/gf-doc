# gset

并发安全集合。集合，即不可重复的一组元素，元素可以为任意类型，常见的如`int`,`string`等。

**使用场景**：

并发安全场景下的集合操作。

**使用方式**：
```go
import "gitee.com/johng/gf/g/container/gset"
```

**方法列表**：[godoc.org/github.com/gogf/gf/g/container/gset](https://godoc.org/github.com/gogf/gf/g/container/gset)
```go
type Set
    func New(safe ...bool) *Set
type IntSet
    func NewIntSet(safe ...bool) *IntSet
    func (this *IntSet) Add(item int) *IntSet
    func (this *IntSet) BatchAdd(items []int) *IntSet
    func (this *IntSet) Clear()
    func (this *IntSet) Contains(item int) bool
    func (this *IntSet) Iterator(f func(v int) bool)
    func (this *IntSet) LockFunc(f func(map[int]struct{}))
    func (this *IntSet) RLockFunc(f func(map[int]struct{}))
    func (this *IntSet) Remove(key int)
    func (this *IntSet) Size() int
    func (this *IntSet) Slice() []int
    func (this *IntSet) String() string
type InterfaceSet
    func NewInterfaceSet(safe ...bool) *InterfaceSet
    func (this *InterfaceSet) Add(item interface{}) *InterfaceSet
    func (this *InterfaceSet) BatchAdd(items []interface{}) *InterfaceSet
    func (this *InterfaceSet) Clear()
    func (this *InterfaceSet) Contains(item interface{}) bool
    func (this *InterfaceSet) Iterator(f func(v interface{}) bool)
    func (this *InterfaceSet) LockFunc(f func(map[interface{}]struct{}))
    func (this *InterfaceSet) RLockFunc(f func(map[interface{}]struct{}))
    func (this *InterfaceSet) Remove(key interface{})
    func (this *InterfaceSet) Size() int
    func (this *InterfaceSet) Slice() []interface{}
    func (this *InterfaceSet) String() string
type StringSet
    func NewStringSet(safe ...bool) *StringSet
    func (this *StringSet) Add(item string) *StringSet
    func (this *StringSet) BatchAdd(items []string) *StringSet
    func (this *StringSet) Clear()
    func (this *StringSet) Contains(item string) bool
    func (this *StringSet) Iterator(f func(v string) bool)
    func (this *StringSet) LockFunc(f func(map[string]struct{}))
    func (this *StringSet) RLockFunc(f func(map[string]struct{}))
    func (this *StringSet) Remove(key string)
    func (this *StringSet) Size() int
    func (this *StringSet) Slice() []string
    func (this *StringSet) String() string
```

使用示例：
```go
package main

import (
    "gitee.com/johng/gf/g/container/gset"
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
