>[danger] # gmap

并发安全Map。

使用方式：
```go
import "gitee.com/johng/gf/g/container/gmap"
```

方法列表：godoc.org/github.com/johng-cn/gf/g/container/gmap
```go
type IntBoolMap
    func NewIntBoolMap() *IntBoolMap
    func (this *IntBoolMap) BatchRemove(keys []int)
    func (this *IntBoolMap) BatchSet(m map[int]bool)
    func (this *IntBoolMap) Clear()
    func (this *IntBoolMap) Clone() *map[int]bool
    func (this *IntBoolMap) Contains(key int) bool
    func (this *IntBoolMap) Get(key int) bool
    func (this *IntBoolMap) GetAndRemove(key int) bool
    func (this *IntBoolMap) GetWithDefault(key int, value bool) bool
    func (this *IntBoolMap) IsEmpty() bool
    func (this *IntBoolMap) Iterator(f func(k int, v bool) bool)
    func (this *IntBoolMap) Keys() []int
    func (this *IntBoolMap) LockFunc(f func(m map[int]bool))
    func (this *IntBoolMap) RLockFunc(f func(m map[int]bool))
    func (this *IntBoolMap) Remove(key int)
    func (this *IntBoolMap) Set(key int, val bool)
    func (this *IntBoolMap) Size() int
type IntIntMap
    func NewIntIntMap() *IntIntMap
    func (this *IntIntMap) BatchRemove(keys []int)
    func (this *IntIntMap) BatchSet(m map[int]int)
    func (this *IntIntMap) Clear()
    func (this *IntIntMap) Clone() *map[int]int
    func (this *IntIntMap) Contains(key int) bool
    func (this *IntIntMap) Get(key int) int
    func (this *IntIntMap) GetAndRemove(key int) int
    func (this *IntIntMap) GetWithDefault(key int, value int) int
    func (this *IntIntMap) IsEmpty() bool
    func (this *IntIntMap) Iterator(f func(k int, v int) bool)
    func (this *IntIntMap) Keys() []int
    func (this *IntIntMap) LockFunc(f func(m map[int]int))
    func (this *IntIntMap) RLockFunc(f func(m map[int]int))
    func (this *IntIntMap) Remove(key int)
    func (this *IntIntMap) Set(key int, val int)
    func (this *IntIntMap) Size() int
    func (this *IntIntMap) Values() []int
type IntInterfaceMap
    func NewIntInterfaceMap() *IntInterfaceMap
    func (this *IntInterfaceMap) BatchRemove(keys []int)
    func (this *IntInterfaceMap) BatchSet(m map[int]interface{})
    func (this *IntInterfaceMap) Clear()
    func (this *IntInterfaceMap) Clone() *map[int]interface{}
    func (this *IntInterfaceMap) Contains(key int) bool
    func (this *IntInterfaceMap) Get(key int) interface{}
    func (this *IntInterfaceMap) GetAndRemove(key int) interface{}
    func (this *IntInterfaceMap) GetBool(key int) bool
    func (this *IntInterfaceMap) GetFloat32(key int) float32
    func (this *IntInterfaceMap) GetFloat64(key int) float64
    func (this *IntInterfaceMap) GetInt(key int) int
    func (this *IntInterfaceMap) GetString(key int) string
    func (this *IntInterfaceMap) GetUint(key int) uint
    func (this *IntInterfaceMap) GetWithDefault(key int, value interface{}) interface{}
    func (this *IntInterfaceMap) IsEmpty() bool
    func (this *IntInterfaceMap) Iterator(f func(k int, v interface{}) bool)
    func (this *IntInterfaceMap) Keys() []int
    func (this *IntInterfaceMap) LockFunc(f func(m map[int]interface{}))
    func (this *IntInterfaceMap) RLockFunc(f func(m map[int]interface{}))
    func (this *IntInterfaceMap) Remove(key int)
    func (this *IntInterfaceMap) Set(key int, val interface{})
    func (this *IntInterfaceMap) Size() int
    func (this *IntInterfaceMap) Values() []interface{}
type IntStringMap
    func NewIntStringMap() *IntStringMap
    func (this *IntStringMap) BatchRemove(keys []int)
    func (this *IntStringMap) BatchSet(m map[int]string)
    func (this *IntStringMap) Clear()
    func (this *IntStringMap) Clone() *map[int]string
    func (this *IntStringMap) Contains(key int) bool
    func (this *IntStringMap) Get(key int) string
    func (this *IntStringMap) GetAndRemove(key int) string
    func (this *IntStringMap) GetWithDefault(key int, value string) string
    func (this *IntStringMap) IsEmpty() bool
    func (this *IntStringMap) Iterator(f func(k int, v string) bool)
    func (this *IntStringMap) Keys() []int
    func (this *IntStringMap) LockFunc(f func(m map[int]string))
    func (this *IntStringMap) RLockFunc(f func(m map[int]string))
    func (this *IntStringMap) Remove(key int)
    func (this *IntStringMap) Set(key int, val string)
    func (this *IntStringMap) Size() int
    func (this *IntStringMap) Values() []string
type InterfaceInterfaceMap
    func NewInterfaceInterfaceMap() *InterfaceInterfaceMap
    func (this *InterfaceInterfaceMap) BatchRemove(keys []interface{})
    func (this *InterfaceInterfaceMap) BatchSet(m map[interface{}]interface{})
    func (this *InterfaceInterfaceMap) Clear()
    func (this *InterfaceInterfaceMap) Clone() *map[interface{}]interface{}
    func (this *InterfaceInterfaceMap) Contains(key interface{}) bool
    func (this *InterfaceInterfaceMap) Get(key interface{}) interface{}
    func (this *InterfaceInterfaceMap) GetAndRemove(key interface{}) interface{}
    func (this *InterfaceInterfaceMap) GetBool(key interface{}) bool
    func (this *InterfaceInterfaceMap) GetFloat32(key interface{}) float32
    func (this *InterfaceInterfaceMap) GetFloat64(key interface{}) float64
    func (this *InterfaceInterfaceMap) GetInt(key interface{}) int
    func (this *InterfaceInterfaceMap) GetString(key interface{}) string
    func (this *InterfaceInterfaceMap) GetUint(key interface{}) uint
    func (this *InterfaceInterfaceMap) GetWithDefault(key interface{}, value interface{}) interface{}
    func (this *InterfaceInterfaceMap) IsEmpty() bool
    func (this *InterfaceInterfaceMap) Iterator(f func(k interface{}, v interface{}) bool)
    func (this *InterfaceInterfaceMap) Keys() []interface{}
    func (this *InterfaceInterfaceMap) LockFunc(f func(m map[interface{}]interface{}))
    func (this *InterfaceInterfaceMap) RLockFunc(f func(m map[interface{}]interface{}))
    func (this *InterfaceInterfaceMap) Remove(key interface{})
    func (this *InterfaceInterfaceMap) Set(key interface{}, val interface{})
    func (this *InterfaceInterfaceMap) Size() int
    func (this *InterfaceInterfaceMap) Values() []interface{}
type StringBoolMap
    func NewStringBoolMap() *StringBoolMap
    func (this *StringBoolMap) BatchRemove(keys []string)
    func (this *StringBoolMap) BatchSet(m map[string]bool)
    func (this *StringBoolMap) Clear()
    func (this *StringBoolMap) Clone() *map[string]bool
    func (this *StringBoolMap) Contains(key string) bool
    func (this *StringBoolMap) Get(key string) bool
    func (this *StringBoolMap) GetAndRemove(key string) bool
    func (this *StringBoolMap) GetWithDefault(key string, value bool) bool
    func (this *StringBoolMap) IsEmpty() bool
    func (this *StringBoolMap) Iterator(f func(k string, v bool) bool)
    func (this *StringBoolMap) Keys() []string
    func (this *StringBoolMap) LockFunc(f func(m map[string]bool))
    func (this *StringBoolMap) RLockFunc(f func(m map[string]bool))
    func (this *StringBoolMap) Remove(key string)
    func (this *StringBoolMap) Set(key string, val bool)
    func (this *StringBoolMap) Size() int
type StringIntMap
    func NewStringIntMap() *StringIntMap
    func (this *StringIntMap) BatchRemove(keys []string)
    func (this *StringIntMap) BatchSet(m map[string]int)
    func (this *StringIntMap) Clear()
    func (this *StringIntMap) Clone() *map[string]int
    func (this *StringIntMap) Contains(key string) bool
    func (this *StringIntMap) Get(key string) int
    func (this *StringIntMap) GetAndRemove(key string) int
    func (this *StringIntMap) GetWithDefault(key string, value int) int
    func (this *StringIntMap) IsEmpty() bool
    func (this *StringIntMap) Iterator(f func(k string, v int) bool)
    func (this *StringIntMap) Keys() []string
    func (this *StringIntMap) LockFunc(f func(m map[string]int))
    func (this *StringIntMap) RLockFunc(f func(m map[string]int))
    func (this *StringIntMap) Remove(key string)
    func (this *StringIntMap) Set(key string, val int)
    func (this *StringIntMap) Size() int
    func (this *StringIntMap) Values() []int
type StringInterfaceMap
    func NewStringInterfaceMap() *StringInterfaceMap
    func (this *StringInterfaceMap) BatchRemove(keys []string)
    func (this *StringInterfaceMap) BatchSet(m map[string]interface{})
    func (this *StringInterfaceMap) Clear()
    func (this *StringInterfaceMap) Clone() *map[string]interface{}
    func (this *StringInterfaceMap) Contains(key string) bool
    func (this *StringInterfaceMap) Get(key string) interface{}
    func (this *StringInterfaceMap) GetAndRemove(key string) interface{}
    func (this *StringInterfaceMap) GetBool(key string) bool
    func (this *StringInterfaceMap) GetFloat32(key string) float32
    func (this *StringInterfaceMap) GetFloat64(key string) float64
    func (this *StringInterfaceMap) GetInt(key string) int
    func (this *StringInterfaceMap) GetString(key string) string
    func (this *StringInterfaceMap) GetUint(key string) uint
    func (this *StringInterfaceMap) GetWithDefault(key string, value interface{}) interface{}
    func (this *StringInterfaceMap) IsEmpty() bool
    func (this *StringInterfaceMap) Iterator(f func(k string, v interface{}) bool)
    func (this *StringInterfaceMap) Keys() []string
    func (this *StringInterfaceMap) LockFunc(f func(m map[string]interface{}))
    func (this *StringInterfaceMap) RLockFunc(f func(m map[string]interface{}))
    func (this *StringInterfaceMap) Remove(key string)
    func (this *StringInterfaceMap) Set(key string, val interface{})
    func (this *StringInterfaceMap) Size() int
    func (this *StringInterfaceMap) Values() []interface{}
type StringStringMap
    func NewStringStringMap() *StringStringMap
    func (this *StringStringMap) BatchRemove(keys []string)
    func (this *StringStringMap) BatchSet(m map[string]string)
    func (this *StringStringMap) Clear()
    func (this *StringStringMap) Clone() *map[string]string
    func (this *StringStringMap) Contains(key string) bool
    func (this *StringStringMap) Get(key string) string
    func (this *StringStringMap) GetAndRemove(key string) string
    func (this *StringStringMap) GetWithDefault(key string, value string) string
    func (this *StringStringMap) IsEmpty() bool
    func (this *StringStringMap) Iterator(f func(k string, v string) bool)
    func (this *StringStringMap) Keys() []string
    func (this *StringStringMap) LockFunc(f func(m map[string]string))
    func (this *StringStringMap) RLockFunc(f func(m map[string]string))
    func (this *StringStringMap) Remove(key string)
    func (this *StringStringMap) Set(key string, val string)
    func (this *StringStringMap) Size() int
    func (this *StringStringMap) Values() []string
type UintInterfaceMap
    func NewUintInterfaceMap() *UintInterfaceMap
    func (this *UintInterfaceMap) BatchRemove(keys []uint)
    func (this *UintInterfaceMap) BatchSet(m map[uint]interface{})
    func (this *UintInterfaceMap) Clear()
    func (this *UintInterfaceMap) Clone() *map[uint]interface{}
    func (this *UintInterfaceMap) Contains(key uint) bool
    func (this *UintInterfaceMap) Get(key uint) interface{}
    func (this *UintInterfaceMap) GetAndRemove(key uint) interface{}
    func (this *UintInterfaceMap) GetBool(key uint) bool
    func (this *UintInterfaceMap) GetFloat32(key uint) float32
    func (this *UintInterfaceMap) GetFloat64(key uint) float64
    func (this *UintInterfaceMap) GetInt(key uint) int
    func (this *UintInterfaceMap) GetString(key uint) string
    func (this *UintInterfaceMap) GetUint(key uint) uint
    func (this *UintInterfaceMap) GetWithDefault(key uint, value interface{}) interface{}
    func (this *UintInterfaceMap) IsEmpty() bool
    func (this *UintInterfaceMap) Iterator(f func(k uint, v interface{}) bool)
    func (this *UintInterfaceMap) Keys() []uint
    func (this *UintInterfaceMap) LockFunc(f func(m map[uint]interface{}))
    func (this *UintInterfaceMap) RLockFunc(f func(m map[uint]interface{}))
    func (this *UintInterfaceMap) Remove(key uint)
    func (this *UintInterfaceMap) Set(key uint, val interface{})
    func (this *UintInterfaceMap) Size() int
    func (this *UintInterfaceMap) Values() []interface{}
```

go语言从1.9版本开始引入了并发安全的sync.Map，我们来看看基准测试结果：
```
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