# gvar

`动态变量`，支持各种内置的数据类型转换，可以作为`interface{}`类型的替代数据类型，并且该类型支持并发安全。

Tips: 框架同时提供了`g.Var`的数据类型，其实也是`gvar.Var`数据类型的别名。

**使用场景**：

使用`interface{}`的场景，各种不固定数据类型格式，或者需要对变量进行频繁的数据类型转换的场景。

**使用方式**：
```go
import "gitee.com/johng/gf/g/container/gvar"
```

**方法列表**：[godoc.org/github.com/johng-cn/gf/g/container/gvar](https://godoc.org/github.com/johng-cn/gf/g/container/gvar)
```go
type Var
    func New(value interface{}, safe ... bool) *Var
    func (v *Var) Bool() bool
    func (v *Var) Bytes() []byte
    func (v *Var) Float32() float32
    func (v *Var) Float64() float64
    func (v *Var) Floats() []float64
    func (v *Var) Int() int
    func (v *Var) Int16() int16
    func (v *Var) Int32() int32
    func (v *Var) Int64() int64
    func (v *Var) Int8() int8
    func (v *Var) Interface() interface{}
    func (v *Var) Interfaces() []interface{}
    func (v *Var) Ints() []int
    func (v *Var) IsNil() bool
    func (v *Var) Set(value interface{})
    func (v *Var) String() string
    func (v *Var) Strings() []string
    func (v *Var) Struct(objPointer interface{}, attrMapping ...map[string]string) error
    func (v *Var) Time(format ...string) time.Time
    func (v *Var) TimeDuration() time.Duration
    func (v *Var) Uint() uint
    func (v *Var) Uint16() uint16
    func (v *Var) Uint32() uint32
    func (v *Var) Uint64() uint64
    func (v *Var) Uint8() uint8
    func (v *Var) Val() interface{}
```

使用示例:

```go
package main

import (
    "gitee.com/johng/gf/g"
    "fmt"
)

func main() {
    var v g.Var

    v.Set("123")

    fmt.Println(v.Val())

    // 基本类型转换
    fmt.Println(v.Int())
    fmt.Println(v.Uint())
    fmt.Println(v.Float64())

    // slice转换
    fmt.Println(v.Ints())
    fmt.Println(v.Floats())
    fmt.Println(v.Strings())

    // struct转换
    type Score struct {
        Value int
    }
    s := new(Score)
    v.Struct(s)
    fmt.Println(s)
}
```

执行后，输出结果为：

```html
123
123
123
123
[123]
[123]
[123]
&{123}
```

