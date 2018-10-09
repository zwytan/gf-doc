[TOC]

# gconv

框架提供了非常强大的类型转换包```gconv```，可以实现将任何数据类型转换为指定的数据类型，对常用基本数据类型之间的无缝转换，同时也支持任意类型到`struct`对象的属性赋值。复杂的数据结构，如自定义的struct类型转换，请参考[gparser包](encoding/gparser/index.md)。

使用方式：
```go
import "gitee.com/johng/gf/g/util/gconv"
```

方法列表： [godoc.org/github.com/johng-cn/gf/g/util/gconv](https://godoc.org/github.com/johng-cn/gf/g/util/gconv)
```go
// 基本类型
func Bool(i interface{}) bool
func Float32(i interface{}) float32
func Float64(i interface{}) float64
func Int(i interface{}) int
func Int16(i interface{}) int16
func Int32(i interface{}) int32
func Int64(i interface{}) int64
func Int8(i interface{}) int8
func String(i interface{}) string
func Uint(i interface{}) uint
func Uint16(i interface{}) uint16
func Uint32(i interface{}) uint32
func Uint64(i interface{}) uint64
func Uint8(i interface{}) uint8

// slice类型
func Bytes(i interface{}) []byte
func Ints(i interface{}) []int
func Floats(i interface{}) []float64
func Strings(i interface{}) []string
func Interfaces(i interface{}) []interface{}

// 时间类型
func Time(i interface{}, format ...string) time.Time
func TimeDuration(i interface{}) time.Duration

// 对象转换
func Struct(params interface{}, objPointer interface{}, attrMapping ...map[string]string) error

// 类型名称转换
func Convert(i interface{}, t string, extraParams ...interface{}) interface{}
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
    fmt.Printf("%10s %v\n", "Int:",        gconv.Int(i))
    fmt.Printf("%10s %v\n", "Int8:",       gconv.Int8(i))
    fmt.Printf("%10s %v\n", "Int16:",      gconv.Int16(i))
    fmt.Printf("%10s %v\n", "Int32:",      gconv.Int32(i))
    fmt.Printf("%10s %v\n", "Int64:",      gconv.Int64(i))
    fmt.Printf("%10s %v\n", "Uint:",       gconv.Uint(i))
    fmt.Printf("%10s %v\n", "Uint8:",      gconv.Uint8(i))
    fmt.Printf("%10s %v\n", "Uint16:",     gconv.Uint16(i))
    fmt.Printf("%10s %v\n", "Uint32:",     gconv.Uint32(i))
    fmt.Printf("%10s %v\n", "Uint64:",     gconv.Uint64(i))
    fmt.Printf("%10s %v\n", "Float32:",    gconv.Float32(i))
    fmt.Printf("%10s %v\n", "Float64:",    gconv.Float64(i))
    fmt.Printf("%10s %v\n", "Bool:",       gconv.Bool(i))
    fmt.Printf("%10s %v\n", "String:",     gconv.String(i))

    fmt.Printf("%10s %v\n", "Bytes:",      gconv.Bytes(i))
    fmt.Printf("%10s %v\n", "Strings:",    gconv.Strings(i))
    fmt.Printf("%10s %v\n", "Ints:",       gconv.Ints(i))
    fmt.Printf("%10s %v\n", "Floats:",     gconv.Floats(i))
    fmt.Printf("%10s %v\n", "Interfaces:", gconv.Interfaces(i))
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
   String: 123
    Bytes: [123]
  Strings: [123]
     Ints: [123]
   Floats: [123]
Interfaces: [123]
```

## Struct转换

### 示例1，基本使用
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
    if err := gconv.Struct(params1, user); err == nil {
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
    if err := gconv.Struct(params2, user); err == nil {
        fmt.Println(user)
    }
}
```
可以看到，我们可以直接通过```Struct```方法将map按照默认规则绑定到struct上，也可以使用```struct tag```的方式进行灵活的设置。此外，```Struct```方法有第三个map参数，用于指定自定义的参数名称到属性名称的映射关系。

执行后，输出结果为：
```shell
&{1 john 123 123}
&{2 smith 456 456}
```

### 示例2，slice基本类型属性

```go
package main

import (
    "gitee.com/johng/gf/g/util/gconv"
    "gitee.com/johng/gf/g"
    "fmt"
)

// 演示slice类型属性的赋值
func main() {
    type User struct {
        Scores []int
    }

    user   := new(User)
    scores := []interface{}{99, 100, 60, 140}

    // 通过map映射转换
    if err := gconv.Struct(g.Map{"Scores" : scores}, user); err != nil {
        fmt.Println(err)
    } else {
        g.Dump(user)
    }

    // 通过变量映射转换，直接slice赋值
    if err := gconv.Struct(scores, user); err != nil {
        fmt.Println(err)
    } else {
        g.Dump(user)
    }
}
```
执行后，输出结果为：
```html
{
	"Scores": [
		99,
		100,
		60,
		140
	]
}
{
	"Scores": [
		99,
		100,
		60,
		140
	]
}
```

### 示例3，struct属性为struct

```go
package main

import (
    "gitee.com/johng/gf/g/util/gconv"
    "gitee.com/johng/gf/g"
    "fmt"
)

func main() {
    type Score struct {
        Name   string
        Result int
    }
    type User struct {
        Scores Score
    }

    user   := new(User)
    scores := map[string]interface{}{
        "Scores" : map[string]interface{}{
            "Name"   : "john",
            "Result" : 100,
        },
    }

    // 嵌套struct转换
    if err := gconv.Struct(scores, user); err != nil {
        fmt.Println(err)
    } else {
        g.Dump(user)
    }
}
```

执行后，输出结果为：

```html
{
	"Scores": {
		"Name": "john",
		"Result": 100
	}
}
```


### 示例4，struct属性为slice，数值为slice

```go
package main

import (
    "gitee.com/johng/gf/g/util/gconv"
    "gitee.com/johng/gf/g"
    "fmt"
)

func main() {
    type Score struct {
        Name   string
        Result int
    }
    type User struct {
        Scores []Score
    }

    user   := new(User)
    scores := map[string]interface{}{
        "Scores" : []interface{}{
            map[string]interface{}{
                "Name"   : "john",
                "Result" : 100,
            },
            map[string]interface{}{
                "Name"   : "smith",
                "Result" : 60,
            },
        },
    }

    // 嵌套struct转换，属性为slice类型，数值为slice map类型
    if err := gconv.Struct(scores, user); err != nil {
        fmt.Println(err)
    } else {
        g.Dump(user)
    }
}
```

执行后，输出结果为：

```html
{
	"Scores": [
		{
			"Name": "john",
			"Result": 100
		},
		{
			"Name": "smith",
			"Result": 60
		}
	]
}
```

### 示例5，struct属性为slice，数值为非slice

```go
package main

import (
    "gitee.com/johng/gf/g/util/gconv"
    "gitee.com/johng/gf/g"
    "fmt"
)

func main() {
    type Score struct {
        Name   string
        Result int
    }
    type User struct {
        Scores []Score
    }

    user   := new(User)
    scores := map[string]interface{}{
        "Scores" : map[string]interface{}{
            "Name"   : "john",
            "Result" : 100,
        },
    }

    // 嵌套struct转换，属性为slice类型，数值为map类型
    if err := gconv.Struct(scores, user); err != nil {
        fmt.Println(err)
    } else {
        g.Dump(user)
    }
}
```
执行后，输出结果为：

```html
{
	"Scores": [
		{
			"Name": "john",
			"Result": 100
		}
	]
}
```


