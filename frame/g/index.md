
[TOC]


# 全局对象

`gf`框架封装了一些常用的数据类型以及对象，可以直接通过```g.*```方便获取。

使用方式：
```go
import "github.com/gogf/gf/g"
```

## 数据类型
```go
// 常用map数据结构
type Map map[string]interface{}

// 常用list数据结构
type List []Map
```

## 常用对象

1. **(单例)配置管理对象**
	```go
    // 参数file未非必需参数，表示指定获取的配置文件管理对象
    func Config(file...string) *gcfg.Config
    ```
3. **(单例)模板引擎对象**
	```go
    func View() *gview.View
    ```
5. **(单例)WEB Server**
	```go
    func Server(name...interface{}) *ghttp.Server
    ```
7. **(单例)TCP Server**
	```go
    func TcpServer(name...interface{}) *gtcp.Server
    ```
9. **(单例)UDP Server**
	```go
    func UdpServer(name...interface{}) *gudp.Server
    ```
11. **(单例)数据库ORM对象**
	```go
    func Database(name...string) *gdb.Db
    // 别名
    func DB(name...string) *gdb.Db
    ```
13. **(单例)Redis客户端对象**
	```go
    func Redis(name...string) *gredis.Redis
    ```


# Debug模式

默认情况下，`gf`框架的各个模块都会开启调试模式，输出一些调试信息，有助于开发者在开发环境中调试定位问题。但是程序正式部署到生产环境之后，这些调试信息往往没有那么必要，并且过多的调试信息也容易在查看重要业务日志的时候影响视觉。因此我们可以通过一些方式关闭掉调试信息。

1. (推荐) 使用 `g.SetDebug(false)` 方法设置；
2. 修改命令行启动参数 - ```gf.glog.debug=false```；
3. 修改指定的环境变量 - ```GF_GLOG_DEBUG=false```；


