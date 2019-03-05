# Map转换

`gconv.Map`支持将任意的`map`或`struct`/`*struct`类型转换为常用的 `map[string]interface{}` 类型。当转换参数为`struct`/`*struct`类型时，支持自动识别`struct`的 `gconv`/`json` 标签，但是可以通过`Map`方法的第二个参数`noTagCheck`进行忽略。
如果转换失败，返回`nil`。

注意：当属性中两个标签 `gconv`和`json` 同时存在时，`gconv`标签的优先级更高。

> 属性标签：当转换`struct`/`*struct`类型时，如果属性带有 `gconv`/`json` 标签，也支持 `-`及`omitempty` 标签属性。当使用 `-` 标签属性时，表示该属性不执行转换；当使用 `omitempty` 标签属性时，表示当属性为空时（空指针`nil`, 数字`0`, 字符串`""`, 空数组`[]`等）不执行转换。具体请查看随后示例。

转换方法：
```go
// Map转换, 支持的类型包括：任意map或struct/*struct
func Map(i interface{}, noTagCheck...bool) map[string]interface{}
```

### 使用示例1，基本示例
```go
package main

import (
    "fmt"
    "github.com/gogf/gf/g/util/gconv"
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

### 使用示例2，属性标签

```go
package main

import (
    "fmt"
    "github.com/gogf/gf/g/util/gconv"
)

func main() {
    type User struct {
        Uid      int
        Name     string `gconv:"-"`
        NickName string `gconv:"nickname, omitempty"`
        Pass1    string `gconv:"password1"`
        Pass2    string `gconv:"password2"`
    }
    user := User{
        Uid      : 100,
        Name     : "john",
        Pass1    : "123",
        Pass2    : "456",
    }
    fmt.Println(gconv.Map(user))
}
```
示例中可以使用`gconv`标签，也使用`json`标签。
执行后，输出结果为：
```
map[Uid:100 password1:123 password2:456]
```