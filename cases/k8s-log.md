# 容器日志搜集工具套件

## 仓库地址
  * [https://gitee.com/johng/k8s-log](https://gitee.com/johng/k8s-log)
  * [https://github.com/johng-cn/k8s-log](https://github.com/johng-cn/k8s-log)

## 关于项目

### 1. 使用到的模块
```go
"gitee.com/johng/gf/g/os/gcron"
"gitee.com/johng/gf/g/os/genv"
"gitee.com/johng/gf/g/os/gfile"
"gitee.com/johng/gf/g/os/glog"
"gitee.com/johng/gf/g/os/gproc"
"gitee.com/johng/gf/g/os/gtime"
"gitee.com/johng/gf/g/util/gconv"
"gitee.com/johng/gf/g/container/gmap"
"gitee.com/johng/gf/g/container/gset"
"gitee.com/johng/gf/g/encoding/gjson"
"gitee.com/johng/gf/g/os/gfsnotify"
"gitee.com/johng/gf/g/os/gmlock"
"gitee.com/johng/gf/g/util/gregex"
"gitee.com/johng/gf/g/database/gkafka"
"gitee.com/johng/gf/g/os/gcache"
"gitee.com/johng/gf/g/container/garray"
"gitee.com/johng/gf/g/util/gstr"
```

### 2. 你能从这个项目了解什么？
* `kafka`客户端的使用；
* `gcron`定时任务模块的使用；
* `gtime`时间模块的使用；
* `garray`排序数组作为内存排序缓冲区的使用；
* 以上各种模块的使用示例；
