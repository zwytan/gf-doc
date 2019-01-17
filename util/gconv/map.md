# Map类型转换

`gconv.Map`支持将`任意的map`或`struct`类型转换为 `map[string]interface{}` 类型，需要注意的是当转换`struct`类型时，支持自动识别struct的`json tag`，但是可以通过`Map`方法的第二个参数`noTagCheck`进行忽略。
如果转换失败，返回`nil`。

```go
// Map转换, 支持的类型包括：任意map或struct
func Map(i interface{}, noTagCheck...bool) map[string]interface{}
```

使用示例：
```go
package main

import (
    "fmt"
    "gitee.com/johng/gf/g/util/gconv"
)

func main() {
    type User struct {
        Uid  int    `json:"uid"`
        Name string `json:"name"`
    }
    // 对象
    fmt.Println(gconv.Map(User{
        Uid      : 1,
        Name     : "john",
    }))
    // 对象指针
    fmt.Println(gconv.Map(&User{
        Uid      : 1,
        Name     : "john",
    }))

    // 任意map类型
    fmt.Println(gconv.Map(map[int]int{
        100 : 10000,
    }))
}
```
执行后，输出结果如下：
```html
map[uid:1 name:john]
map[uid:1 name:john]
map[100:10000]
```
