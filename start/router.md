# 路由注册
`/router/router.go`
```go
package router

import (
    "gitee.com/johng/gf-demos/app/controller/user"
    "gitee.com/johng/gf/g"
)

// 统一路由注册.
func init() {
    // 用户模块 路由注册 - 使用执行对象注册方式
    g.Server().BindObject("/user", new(ctl_user.Controller))
}
```

这里使用执行对象注册方式。