[TOC]

# 项目设计

## 数据库设计
`/docfile/sql/create.sql`
```sql
CREATE TABLE `user` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT '用户ID',
  `passport` varchar(45) NOT NULL COMMENT '账号',
  `password` char(32) NOT NULL COMMENT '密码',
  `nickname` varchar(45) NOT NULL COMMENT '昵称',
  `create_time` timestamp NOT NULL COMMENT '创建时间/注册时间',
  PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;
```



# 业务逻辑

注意所有的业务逻辑程序都应当存放在`app`目录下。

## 控制器
`/app/controller/user/user.go`
```go
package ctl_user

import (
    "gitee.com/johng/gf-demos/app/library/response"
    "gitee.com/johng/gf-demos/app/library/user"
    "gitee.com/johng/gf/g/net/ghttp"
    "gitee.com/johng/gf/g/util/gvalid"
)

// 用户API管理对象
type Controller struct { }

// 用户注册接口
func (c *Controller) SignUp(r *ghttp.Request) {
    if err := lib_user.SignUp(r.GetPostMap()); err != nil {
        lib_response.Json(r, 1, err.Error())
    } else {
        lib_response.Json(r, 0, "ok")
    }
}

// 用户登录接口
func (c *Controller) SignIn(r *ghttp.Request) {
    data  := r.GetPostMap()
    rules := map[string]string {
        "passport"  : "required",
        "password"  : "required",
    }
    msgs  := map[string]interface{} {
        "passport" : "账号不能为空",
        "password" : "密码不能为空",
    }
    if e := gvalid.CheckMap(data, rules, msgs); e != nil {
        lib_response.Json(r, 1, e.String())
    }
    if err := lib_user.SignIn(data["passport"], data["password"], r.Session); err != nil {
        lib_response.Json(r, 1, err.Error())
    } else {
        lib_response.Json(r, 0, "ok")
    }
}

// 判断用户是否已经登录
func (c *Controller) IsSignedIn(r *ghttp.Request) {
    if lib_user.IsSignedIn(r.Session) {
        lib_response.Json(r, 0, "ok")
    } else {
        lib_response.Json(r, 1, "")
    }
}

// 用户注销/退出接口
func (c *Controller) SignOut(r *ghttp.Request) {
    lib_user.SignOut(r.Session)
    lib_response.Json(r, 0, "ok")
}

// 检测用户账号接口(唯一性校验)
func (c *Controller) CheckPassport(r *ghttp.Request) {
    passport := r.Get("passport")
    if e := gvalid.Check(passport, "required", "请输入账号"); e != nil {
        lib_response.Json(r, 1, e.String())
    }
    if lib_user.CheckPassport(passport) {
        lib_response.Json(r, 0, "ok")
    }
    lib_response.Json(r, 1, "账号已经存在")
}

// 检测用户昵称接口(唯一性校验)
func (c *Controller) CheckNickName(r *ghttp.Request) {
    nickname := r.Get("nickname")
    if e := gvalid.Check(nickname, "required", "请输入昵称"); e != nil {
        lib_response.Json(r, 1, e.String())
    }
    if lib_user.CheckNickName(r.Get("nickname")) {
        lib_response.Json(r, 0, "ok")
    }
    lib_response.Json(r, 1, "昵称已经存在")
}
```

## 逻辑封装

### 用户逻辑
`/app/library/user/user.go`
```go
package lib_user

import (
    "errors"
    "fmt"
    "gitee.com/johng/gf/g"
    "gitee.com/johng/gf/g/net/ghttp"
    "gitee.com/johng/gf/g/os/gtime"
    "gitee.com/johng/gf/g/util/gvalid"
)

const (
    USER_SESSION_MARK = "user_info"
)

var (
    // 表对象
    table = g.DB().Table("user")
)

// 用户注册
func SignUp(data g.MapStrStr) error {
    // 数据校验
    rules := []string {
        "passport @required|length:6,16#账号不能为空|账号长度应当在:min到:max之间",
        "password2@required|length:6,16#请输入确认密码|密码长度应当在:min到:max之间",
        "password @required|length:6,16|same:password2#密码不能为空|密码长度应当在:min到:max之间|两次密码输入不相等",
    }
    if e := gvalid.CheckMap(data, rules); e != nil {
        return errors.New(e.String())
    }
    if _, ok := data["nickname"]; !ok {
        data["nickname"] = data["passport"]
    }
    // 唯一性数据检查
    if !CheckPassport(data["passport"]) {
        return errors.New(fmt.Sprintf("账号 %s 已经存在", data["passport"]))
    }
    if !CheckPassport(data["nickname"]) {
        return errors.New(fmt.Sprintf("昵称 %s 已经存在", data["nickname"]))
    }
    // 记录账号创建/注册时间
    if _, ok := data["create_time"]; !ok {
        data["create_time"] = gtime.Now().String()
    }
    if _, err := table.Filter().Data(data).Save(); err != nil {
        return err
    }
    return nil
}

// 判断用户是否已经登录
func IsSignedIn(session *ghttp.Session) bool {
    return session.Contains(USER_SESSION_MARK)
}

// 用户登录，成功返回用户信息，否则返回nil; passport应当会md5值字符串
func SignIn(passport, password string, session *ghttp.Session) error {
    record, err := table.Where("passport=? and password=?", passport, password).One()
    if err != nil {
        return err
    }
    if record == nil {
        return errors.New("账号或密码错误")
    }
    session.Set(USER_SESSION_MARK, record)
    return nil
}

// 用户注销
func SignOut(session *ghttp.Session) {
    session.Remove(USER_SESSION_MARK)
}

// 检查账号是否符合规范(目前仅检查唯一性),存在返回false,否则true
func CheckPassport(passport string) bool {
    if i, err := table.Where("passport", passport).Count(); err != nil {
        return false
    } else {
        return i == 0
    }
}

// 检查昵称是否符合规范(目前仅检查唯一性),存在返回false,否则true
func CheckNickName(nickname string) bool {
    if i, err := table.Where("nickname", nickname).Count(); err != nil {
        return false
    } else {
        return i == 0
    }
}
```

### 工具类包
`/app/library/response/response.go`
```go
package lib_response

import (
    "gitee.com/johng/gf/g"
    "gitee.com/johng/gf/g/net/ghttp"
)

// 标准返回结果数据结构封装。
// 返回固定数据结构的JSON:
// err:  错误码(0:成功, 1:失败, >1:错误码);
// msg:  请求结果信息;
// data: 请求结果,根据不同接口返回结果的数据结构不同;
func Json(r *ghttp.Request, err int, msg string, data...interface{}) {
    responseData := interface{}(nil)
    if len(data) > 0 {
        responseData = data[0]
    }
    r.Response.WriteJson(g.Map{
        "err"  : err,
        "msg"  : msg,
        "data" : responseData,
    })
    r.Exit()
}
```

# 项目配置

## go.mod
`/go.mod`
```
module gitee.com/johng/gf-demos

require gitee.com/johng/gf latest
```
其中注意`module`名称设置为`gitee.com/johng/gf-demos`。


## 配置文件
`/config/config.toml`
```toml
# 应用系统设置
[setting]
    logpath = "/tmp/log/gf-demos"

# 数据库连接
[database]
    [[database.default]]
        host = "127.0.0.1"
        port = "3306"
        user = "root"
        pass = "12345678"
        name = "test"
        type = "mysql"
```

## 路由注册
`/router/router.go`
```go
package router

import (
    "gitee.com/johng/gf-demos/app/controller/user"
    "gitee.com/johng/gf/g"
)

// 统一路由注册.
func init() {
	s := g.Server()

	// 某些浏览器直接请求favicon.ico文件，特别是产生404时
	s.SetRewrite("/favicon.ico", "/resource/image/favicon.ico")

    // 用户模块 路由注册 - 使用执行对象注册方式
    s.BindObject("/user", new(ctl_user.Controller))
}
```

## 启动设置
`/boot/boot.go`
```go
package boot

import (
    "gitee.com/johng/gf/g"
    "gitee.com/johng/gf/g/net/ghttp"
)

// 用于应用初始化。
func init() {
    s := g.Server()
    s.SetNameToUriType(ghttp.NAME_TO_URI_TYPE_ALLLOWER)
    s.SetErrorLogEnabled(true)
    s.SetAccessLogEnabled(true)
    s.SetPort(8199)
}
```


