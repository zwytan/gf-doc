# 路由注册
`/router/router.go`
```go
package router

import (
    "github.com/gogf/gf-demos/app/api/user"
    "github.com/gogf/gf/g"
)

// 统一路由注册.
func init() {
    // 用户模块 路由注册 - 使用执行对象注册方式
    g.Server().BindObject("/user", new(api_user.Controller))
}
```

这里使用执行对象注册方式。