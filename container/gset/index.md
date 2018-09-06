# gset

并发安全集合。集合，即不可重复的一组元素，元素可以为任意类型，常见的如`int`,`string`等。

使用场景：

并发安全场景下的集合操作。

使用方式：
```go
import "gitee.com/johng/gf/g/container/gset"
```

方法列表：[godoc.org/github.com/johng-cn/gf/g/container/gset](https://godoc.org/github.com/johng-cn/gf/g/container/gset)
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

文档未完待续。
