>[danger] # garray

并发安全数组。

使用方式：
```go
import "gitee.com/johng/gf/g/container/garray"
```

方法列表（[API文档](https://godoc.org/github.com/johng-cn/gf/g/container/garray)）：

普通并发安全数组。
```go
type Array
    func NewArray(size int, cap ...int) *Array
    func (a *Array) Append(value interface{})
    func (a *Array) Clear()
    func (a *Array) Get(index int) interface{}
    func (a *Array) Insert(index int, value interface{})
    func (a *Array) Len() int
    func (a *Array) LockFunc(f func(array []interface{}))
    func (a *Array) RLockFunc(f func(array []interface{}))
    func (a *Array) Remove(index int)
    func (a *Array) Set(index int, value interface{})
    func (a *Array) Slice() []interface{}
type IntArray
    func NewIntArray(size int, cap ...int) *IntArray
    func (a *IntArray) Append(value int)
    func (a *IntArray) Clear()
    func (a *IntArray) Get(index int) int
    func (a *IntArray) Insert(index int, value int)
    func (a *IntArray) Len() int
    func (a *IntArray) LockFunc(f func(array []int))
    func (a *IntArray) RLockFunc(f func(array []int))
    func (a *IntArray) Remove(index int)
    func (a *IntArray) Set(index int, value int)
    func (a *IntArray) Slice() []int
type StringArray
    func NewStringArray(size int, cap ...int) *StringArray
    func (a *StringArray) Append(value string)
    func (a *StringArray) Clear()
    func (a *StringArray) Get(index int) string
    func (a *StringArray) Insert(index int, value string)
    func (a *StringArray) Len() int
    func (a *StringArray) LockFunc(f func(array []string))
    func (a *StringArray) RLockFunc(f func(array []string))
    func (a *StringArray) Remove(index int)
    func (a *StringArray) Set(index int, value string)
    func (a *StringArray) Slice() []string
```
排序的并发安全数组，支持唯一数据项矫正。
```go
type SortedArray
    func NewSortedArray(size int, cap int, compareFunc func(v1, v2 interface{}) int) *SortedArray
    func (a *SortedArray) Add(value interface{})
    func (a *SortedArray) Clear()
    func (a *SortedArray) Get(index int) interface{}
    func (a *SortedArray) Len() int
    func (a *SortedArray) LockFunc(f func(array []interface{}))
    func (a *SortedArray) RLockFunc(f func(array []interface{}))
    func (a *SortedArray) Remove(index int)
    func (a *SortedArray) Search(value interface{}) (int, int)
    func (a *SortedArray) SetUnique(unique bool)
    func (a *SortedArray) Slice() []interface{}
type SortedIntArray
    func NewSortedIntArray(size int, cap ...int) *SortedIntArray
    func (a *SortedIntArray) Add(value int)
    func (a *SortedIntArray) Clear()
    func (a *SortedIntArray) Get(index int) int
    func (a *SortedIntArray) Len() int
    func (a *SortedIntArray) LockFunc(f func(array []int))
    func (a *SortedIntArray) RLockFunc(f func(array []int))
    func (a *SortedIntArray) Remove(index int)
    func (a *SortedIntArray) Search(value int) (int, int)
    func (a *SortedIntArray) SetUnique(unique bool)
    func (a *SortedIntArray) Slice() []int
type SortedStringArray
    func NewSortedStringArray(size int, cap ...int) *SortedStringArray
    func (a *SortedStringArray) Add(value string)
    func (a *SortedStringArray) Clear()
    func (a *SortedStringArray) Get(index int) string
    func (a *SortedStringArray) Len() int
    func (a *SortedStringArray) LockFunc(f func(array []string))
    func (a *SortedStringArray) RLockFunc(f func(array []string))
    func (a *SortedStringArray) Remove(index int)
    func (a *SortedStringArray) Search(value string) (int, int)
    func (a *SortedStringArray) SetUnique(unique bool)
    func (a *SortedStringArray) Slice() []string
```


