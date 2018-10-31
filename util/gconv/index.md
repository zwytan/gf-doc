[TOC]

框架提供了非常强大的类型转换包```gconv```，可以实现将任何数据类型转换为指定的数据类型，对常用基本数据类型之间的无缝转换，同时也支持任意类型到`struct`对象的属性赋值。由于`gconv`模块内部大量使用了断言而非反射(仅`struct`转换使用到了反射)，因此执行的效率非常高。

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

// 根据类型名称执行基本类型转换(非struct转换))
func Convert(i interface{}, t string, extraParams ...interface{}) interface{}
```
# 基本使用
常用基本类型的转换方法比较简单，我们这里使用一个例子来演示转换方法的使用及效果。
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

# Struct转换

`struct`转换方法只有一个，定义如下：
```go
func Struct(params interface{}, objPointer interface{}, attrMapping ...map[string]string) error
```
其中：
1. `params`为需要转换到`struct`的变量参数，可以为任意数据类型，常见的数据类型为`map`；
1. `objPointer`为需要执行转的目标`struct`对象，这个参数必须为该`struct`的对象指针，转换成功后该对象的属性将会更新；
1. `attrMapping`为自定义的`map键名`到`strcut属性`之间的映射关系，此时`params`参数必须为map类型，否则该参数无意义；


## 转换规则
`gconv`模块的`struct`转换特性非常强大，支持任意数据类型到`struct`属性的映射转换。在没有提供自定义`attrMapping`转换规则的情况下，默认的转换规则如下：
1. `struct`中需要匹配的属性必须为**`公开属性`**(首字母大小)；
2. 根据`params`类型的不同，逻辑会有不同：
    - `params`参数为`map`: 键名会自动按照**`不区分大小写`**的形式与`struct`属性进行匹配；
    - `params`参数为其他类型: 将会把该变量值与`struct`的第一个属性进行匹配；
    - 此外，如果`struct`的属性为复杂数据类型如`slice`,`map`,`strcut`那么会进行递归匹配赋值； 
3. 如果匹配成功，那么将键值赋值给属性，如果无法匹配，那么忽略；

以下是几个匹配的示例：
```html
map键名    struct属性     是否匹配
name       Name           match
Email      Email          match
nickname   NickName       match
NICKNAME   NickName       match
Nick-Name  NickName       not match
nick_name  NickName       not match
nick_name  Nick_Name      match
```


## 示例1，基本使用
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

    // 使用默认映射规则绑定属性值到对象
    user     = new(User)
    params1 := g.Map{
        "uid"   : 1,
        "Name"  : "john",
        "PASS1" : "123",
        "PaSs2" : "456",
    }
    if err := gconv.Struct(params1, user); err == nil {
        fmt.Println(user)
    }

    // 使用struct tag映射绑定属性值到对象
    user     = new(User)
    params2 := g.Map {
        "uid"       : 2,
        "name"      : "smith",
        "password1" : "111",
        "password2" : "222",
    }
    if err := gconv.Struct(params2, user); err == nil {
        fmt.Println(user)
    }
}
```
可以看到，我们可以直接通过```Struct```方法将map按照默认规则绑定到struct上，也可以使用```struct tag```的方式进行灵活的设置。此外，```Struct```方法有第三个map参数，用于指定自定义的参数名称到属性名称的映射关系。

执行后，输出结果为：
```shell
&{1 john 123 456}
&{2 smith 111 222}
```

## 示例2，复杂类型转换
### 1. slice基本类型属性

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

### 2. struct属性为struct

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


### 3. struct属性为slice，数值为slice

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

### 4. struct属性为slice，数值为非slice

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


