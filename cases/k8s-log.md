# 容器日志搜集工具套件

## 仓库地址
  * [https://gitee.com/johng/k8s-log](https://gitee.com/johng/k8s-log)
  * [https://github.com/johng-cn/k8s-log](https://github.com/johng-cn/k8s-log)

## 关于项目

### 1. 使用到的模块
```go
"github.com/gogf/gf/g/os/gcron"
"github.com/gogf/gf/g/os/genv"
"github.com/gogf/gf/g/os/gfile"
"github.com/gogf/gf/g/os/glog"
"github.com/gogf/gf/g/os/gproc"
"github.com/gogf/gf/g/os/gtime"
"github.com/gogf/gf/g/util/gconv"
"github.com/gogf/gf/g/container/gmap"
"github.com/gogf/gf/g/container/gset"
"github.com/gogf/gf/g/encoding/gjson"
"github.com/gogf/gf/g/os/gfsnotify"
"github.com/gogf/gf/g/os/gmlock"
"github.com/gogf/gf/g/text/gregex"
"github.com/gogf/gf/g/database/gkafka"
"github.com/gogf/gf/g/os/gcache"
"github.com/gogf/gf/g/container/garray"
"github.com/gogf/gf/g/util/gstr"
```

### 2. 你能从这个项目了解什么？
* `kafka`客户端的使用；
* `gcron`定时任务模块的使用；
* `gtime`时间模块的使用；
* `garray`排序数组作为内存排序缓冲区的使用；
* 以上各种模块的使用示例；
