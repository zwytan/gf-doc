# gkvdb高性能Key-Value嵌入式事务数据库

## 仓库地址
  * [https://gitee.com/johng/gkvdb](https://gitee.com/johng/gkvdb)
  * [https://github.com/johng-cn/gkvdb](https://github.com/johng-cn/gkvdb)

## 关于项目

### 1. 使用到的模块
```go
"gitee.com/johng/gf/g/os/gfile"
"gitee.com/johng/gf/g/encoding/ghash"
"gitee.com/johng/gf/g/container/gmap"
"gitee.com/johng/gf/g/container/gtype"
"gitee.com/johng/gf/g/os/gmlock"
"gitee.com/johng/gf/g/os/grpool"
"gitee.com/johng/gf/g/container/glist"
"gitee.com/johng/gf/g/os/gcache"
"gitee.com/johng/gkvdb/gkvdb/gfilespace"
"gitee.com/johng/gf/g/os/gtime"
```

### 2. 你能从这个项目了解什么？
* 《Key-Value数据库是如何炼成的》
* 常用数据结构操作，二进制文件操作，并发安全控制；
* 缓存控制、内存锁、文件碎片管理、文件指针池、Goroutine池等等；
* 等等等等；
