<div align=center>
<img src="cover.png" width="250"/>
</div>

GF(Go Frame)是一款模块化、松耦合、轻量级、高性能的Go语言应用开发框架。支持热重启、热更新、多域名、多端口、多服务、HTTP/HTTPS、动态路由等特性，并提供了Web服务开发的系列核心组件，如：Router、Cookie、Session、服务注册、配置管理、模板引擎、数据校验、分页管理、数据库ORM等等等等，并且提供了数十个实用开发模块集，如：缓存、日志、时间、命令行、二进制、文件锁、对象池、连接池、数据编码、进程管理、进程通信、TCP/UDP组件、并发安全容器、Goroutine池等等等等等等。


# 安装
```html
go get -u gitee.com/johng/gf
```

# 限制
```html
golang版本 >= 1.9.2
```

# 特点
1. 轻量级、高性能，模块化、松耦合设计，丰富的开发模块；
1. 热重启、热更新特性，并支持Web界面及命令行管理接口；
1. 专业的技术交流群，完善的开发文档及示例代码，良好的中文化支持；
1. 支持多种形式的服务注册特性，强大灵活高效的路由控制管理；
1. 支持服务事件回调注册功能，可供选择的`pprof`性能分析模块；
1. 支持配置文件及模板文件的自动检测更新机制，即修改即生效；
1. 支持自定义日期时间格式的时间模块，类似PHP日期时间格式化；
1. 强大的数据/表单校验模块，支持常用的40种及自定义校验规则；
1. 强大的网络通信TCP/UDP组件，并提供TCP连接池特性，简便高效；
1. 提供了对基本数据类型的并发安全封装，提供了常用的数据结构容器；
1. 支持`Go变量/Json/Xml/Yml/Toml`任意数据格式之间的相互转换及创建；
1. 强大的数据库`ORM`，支持应用层级的集群管理、读写分离、负载均衡，查询缓存、方法及链式ORM操作；
12. 更多特点请查阅框架手册和源码；

`gf`是开源的，免费的，基于MIT协议进行分发，开源项目地址：
- **Gitee**( https://gitee.com/johng/gf )
- **Github**( https://github.com/johng-cn/gf )


框架API文档地址：[https://godoc.org/github.com/johng-cn/gf](https://godoc.org/github.com/johng-cn/gf)

使用中有任何问题/建议，欢迎加入技术QQ群交流：`116707870`。如有优秀的gf框架使用案例，欢迎联系作者将地址展示到项目库中，您的牛逼将被世人所瞻仰。

**当前文档版本 v1.0.898 stable**

# 源码目录

```html
.
├── g			        框架源码目录
│   ├── container           并发安全容器
│   │   ├── garray          并发安全数组
│   │   ├── gchan           优雅的chan操作
│   │   ├── glist           并发安全链表
│   │   ├── gmap            并发安全Map
│   │   ├── gpool           对象复用池
│   │   ├── gqueue          并发安全队列
│   │   ├── gring           并发安全的环
│   │   ├── gset            并发安全集合
│   │   ├── gtype           并发安全类型
│   │   └── gvar            通用动态变量
│   ├── crypto          加密/解密模块
│   │   ├── gcrc32          CRC32
│   │   ├── gaes            AES
│   │   ├── gdes            DES
│   │   ├── gmd5            MD5
│   │   └── gsha1           SHA1
│   ├── database        数据库管理
│   │   ├── gdb             通用数据库管理
│   │   ├── gkafka          Kafka客户端
│   │   └── gredis          Redis客户端
│   ├── encoding        编码/解码模块
│   │   ├── gbase64         BASE64
│   │   ├── gbinary         二进制操作
│   │   ├── gcharset        字符编码转换
│   │   ├── gcompress       压缩/解压
│   │   ├── ghash           哈希函数
│   │   ├── ghtml           HTML编码/解析
│   │   ├── gjson           JSON
│   │   ├── gparser         通用数据编码/解析
│   │   ├── gtoml           TOML
│   │   ├── gurl            URL
│   │   ├── gxml            XML
│   │   └── gyaml           YAML
│   ├── frame           常用框架管理
│   │   ├── gins            单例管理
│   │   └── gmvc            MVC开发模式
│   ├── net             网络通信管理
│   │   ├── ghttp           HTTP组件
│   │   ├── gipv4           IPV4
│   │   ├── gipv6           IPV6
│   │   ├── gscanner        局域网扫描
│   │   ├── gsmtp           SMTP
│   │   ├── gtcp            TCP组件
│   │   └── gudp            UDP组件
│   ├── os              系统管理模块
│   │   ├── gcache          单进程高速缓存
│   │   ├── gcfg            配置文件管理器
│   │   ├── gcmd            命令行参数管理
│   │   ├── gcron           定时任务
│   │   ├── genv            环境变量
│   │   ├── gfcache         文件缓存
│   │   ├── gfile           文件管理
│   │   ├── gfpool          文件指针池
│   │   ├── gflock          文件锁
│   │   ├── gfsnotify       文件监控
│   │   ├── glog            日志管理
│   │   ├── gmlock          内存锁
│   │   ├── gproc           进程管理通信
│   │   ├── grpool          协程池
│   │   ├── gtime           时间日期
│   │   └── gview           模板引擎
│   └── util            常用工具模块
│       ├── gconv           基本类型转换
│       ├── gpage           分页管理
│       ├── grand           随机数管理
│       ├── gregx           正则表达式
│       ├── gutil           其他工具方法
│       └── gvalid          表单/数据校验
├── geg                 框架示例代码
├── third               第三方依赖包
└── version.go          当前框架版本
```

