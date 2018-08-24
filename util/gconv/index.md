# gconv

框架提供了非常强大的类型转换包```gconv```，可以实现将任何数据类型转换为指定的数据类型，同时也对常用基本数据类型的无缝转换。复杂的数据类型，如自定义的struct类型转换，请参考[gparser包](encoding/gparser/index.md)。

使用方式：
```go
import "gitee.com/johng/gf/g/util/gconv"
```

方法列表： [godoc.org/github.com/johng-cn/gf/g/util/gconv](https://godoc.org/github.com/johng-cn/gf/g/util/gconv)
```
func Bool(i interface{}) bool
func Bytes(i interface{}) []byte
func Convert(i interface{}, t string) interface{}
func Float32(i interface{}) float32
func Float64(i interface{}) float64
func Int(i interface{}) int
func Int16(i interface{}) int16
func Int32(i interface{}) int32
func Int64(i interface{}) int64
func Int8(i interface{}) int8
func String(i interface{}) string
func Strings(i interface{}) []string
func Time(i interface{}) time.Time
func TimeDuration(i interface{}) time.Duration
func Uint(i interface{}) uint
func Uint16(i interface{}) uint16
func Uint32(i interface{}) uint32
func Uint64(i interface{}) uint64
func Uint8(i interface{}) uint8

func MapToStruct(params map[string]interface{}, object interface{}, mapping ...map[string]string) error
```
## 基本使用

使用示例：

```go
package main

import (
    "fmt"
    "gitee.com/johng/gf/g/util/gconv"
)

func main() {
    i := 123
    fmt.Printf("%10s %v\n", "Int:",     gconv.Int(i))
    fmt.Printf("%10s %v\n", "Int8:",    gconv.Int8(i))
    fmt.Printf("%10s %v\n", "Int16:",   gconv.Int16(i))
    fmt.Printf("%10s %v\n", "Int32:",   gconv.Int32(i))
    fmt.Printf("%10s %v\n", "Int64:",   gconv.Int64(i))
    fmt.Printf("%10s %v\n", "Uint:",    gconv.Uint(i))
    fmt.Printf("%10s %v\n", "Uint8:",   gconv.Uint8(i))
    fmt.Printf("%10s %v\n", "Uint16:",  gconv.Uint16(i))
    fmt.Printf("%10s %v\n", "Uint32:",  gconv.Uint32(i))
    fmt.Printf("%10s %v\n", "Uint64:",  gconv.Uint64(i))
    fmt.Printf("%10s %v\n", "Float32:", gconv.Float32(i))
    fmt.Printf("%10s %v\n", "Float64:", gconv.Float64(i))
    fmt.Printf("%10s %v\n", "Bool:",    gconv.Bool(i))
    fmt.Printf("%10s %v\n", "Bytes:",   gconv.Bytes(i))
    fmt.Printf("%10s %v\n", "String:",  gconv.String(i))
    fmt.Printf("%10s %v\n", "Strings:", gconv.Strings(i))
}
```
执行后，输出结果为：
```
      Int: 123
     Int8: 123
    Int16: 123
    Int32: 123
    Int64: 123
     Uint: 123
    Uint8: 123
   Uint16: 123
   Uint32: 123
   Uint64: 123
  Float32: 123
  Float64: 123
     Bool: true
    Bytes: [123]
   String: 123
  Strings: [123]
```

## MapToStruct

```go
package main

import (
    "fmt"
    "gitee.com/johng/gf/g"
    "gitee.com/johng/gf/g/util/gconv"
)

type User struct {
    Uid   int
    Name  string
    Pass1 string `gconv:"password1"`
    Pass2 string `gconv:"password2"`
}

func main() {
    user    := (*User)(nil)

    // 使用map直接映射绑定属性值到对象
    user     = new(User)
    params1 := g.Map{
        "uid"   : 1,
        "name"  : "john",
        "pass1" : "123",
        "pass2" : "123",
    }
    if err := gconv.MapToStruct(params1, user); err == nil {
        fmt.Println(user)
    }

    // 使用struct tag映射绑定属性值到对象
    user     = new(User)
    params2 := g.Map {
        "uid"       : 2,
        "name"      : "smith",
        "password1" : "456",
        "password2" : "456",
    }
    if err := gconv.MapToStruct(params2, user); err == nil {
        fmt.Println(user)
    }
}
```
可以看到，我们可以直接通过```MapToStruct```方法将map按照默认规则绑定到struct上，也可以使用```struct tag```的方式进行灵活的设置。此外，```MapToStruct```方法有第三个map参数，用于指定自定义的参数名称到属性名称的映射关系。

执行后，输出结果为：
```shell
&{1 john 123 123}
&{2 smith 456 456}
```

