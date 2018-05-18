>[danger] # gset

并发安全Set。

使用方式：
```go
import "gitee.com/johng/gf/g/container/gset"
```

方法列表：godoc.org/github.com/johng-cn/gf/g/container/gset
```go
type IntSet
    func NewIntSet() *IntSet
    func (this *IntSet) Add(item int) *IntSet
    func (this *IntSet) BatchAdd(items []int) *IntSet
    func (this *IntSet) Clear()
    func (this *IntSet) Contains(item int) bool
    func (this *IntSet) Iterator(f func(v int))
    func (this *IntSet) Remove(key int)
    func (this *IntSet) Size() int
    func (this *IntSet) Slice() []int
    func (this *IntSet) String() string
type InterfaceSet
    func NewInterfaceSet() *InterfaceSet
    func (this *InterfaceSet) Add(item interface{}) *InterfaceSet
    func (this *InterfaceSet) BatchAdd(items []interface{}) *InterfaceSet
    func (this *InterfaceSet) Clear()
    func (this *InterfaceSet) Contains(item interface{}) bool
    func (this *InterfaceSet) Iterator(f func(v interface{}))
    func (this *InterfaceSet) Remove(key interface{})
    func (this *InterfaceSet) Size() int
    func (this *InterfaceSet) Slice() []interface{}
    func (this *InterfaceSet) String() string
type StringSet
    func NewStringSet() *StringSet
    func (this *StringSet) Add(item string) *StringSet
    func (this *StringSet) BatchAdd(items []string) *StringSet
    func (this *StringSet) Clear()
    func (this *StringSet) Contains(item string) bool
    func (this *StringSet) Iterator(f func(v string))
    func (this *StringSet) Remove(key string)
    func (this *StringSet) Size() int
    func (this *StringSet) Slice() []string
    func (this *StringSet) String() string
```