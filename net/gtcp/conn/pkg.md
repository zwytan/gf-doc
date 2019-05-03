
[TOC]

# 消息包处理

`gtcp`提供了许多方便的原生操作连接数据的方法，但是在绝大多数的应用场景中，开发者需要自己设计数据结构，并进行封包/解包处理，复杂的网络通信环境中很容易出现**粘包/多包、少包/断包**的情况。因此`gtcp`也提供了简单的数据协议，方便开发者进行消息包交互，开发者不再需要担心消息包的处理细节，包括封包/解包处理，这一切复杂的逻辑`gtcp`已经帮你处理好了。

> `粘包/多包`情况（多个连续的包被一次性读取到缓冲区）时`gtcp`将会自动进行分包处理；`少包/断包`情况（一个包中插入了其他包数据，一个包被截断、拆分为多个部分，造成包数据非连续）时`gtcp`将会直接丢弃非连续、错误的包。

## 简单协议

`gtcp`模块提供了简单轻量级数据交互协议，效率非常高，协议格式如下：
```html
总长度(24bit)|校验码(32bit)|数据(变长)
```
1. 总长度：为24位(3字节)，用于标识该消息体的大小，单位为字节，包含自身的3字节；
1. 校验码：为32位(4字节)，数据字段通过`gtcp.Checksum`校验方法生成的校验码值，用于校验数据完整性；
1. 数据：变长，根据总长度可以知道，数据最大长度不能超过`0xFFFFFF - 7 = 16777208 bytes = 16383 KB = 15MB`，即最大数据字段不能超过`15MB`；

简单协议由`gtcp`封装实现，如果开发者客户端和服务端如果都使用`gtcp`模块来通信则无需关心协议实现，专注`数据`字段封装/解析实现即可。如果涉及和其他开发语言对接，则需要按照该协议实现对接即可（注意`Checksum`校验和方法的实现），由于简单协议非常简单轻量级，因此对接成本很低。

## 操作方法

https://godoc.org/github.com/gogf/gf/g/net/gtcp

```go
type Conn
    func (c *Conn) RecvPkg(retry ...Retry) (result []byte, err error)
    func (c *Conn) RecvPkgWithTimeout(timeout time.Duration, retry ...Retry) ([]byte, error)
    func (c *Conn) SendPkg(data []byte, retry ...Retry) error
    func (c *Conn) SendPkgWithTimeout(data []byte, timeout time.Duration, retry ...Retry) error
    func (c *Conn) SendRecvPkg(data []byte, retry ...Retry) ([]byte, error)
    func (c *Conn) SendRecvPkgWithTimeout(data []byte, timeout time.Duration, retry ...Retry) ([]byte, error)
```
可以看到，消息包方法命名是在原有的基本连接操作方法中加上了`Pkg`关键词便于区分。

## 使用示例1，基本使用

```go
package main

import (
	"fmt"
	"github.com/gogf/gf/g/net/gtcp"
	"github.com/gogf/gf/g/os/glog"
	"github.com/gogf/gf/g/util/gconv"
	"time"
)

func main() {
	// Server
	go gtcp.NewServer("127.0.0.1:8999", func(conn *gtcp.Conn) {
		defer conn.Close()
		for {
			data, err := conn.RecvPkg()
			if err != nil {
				fmt.Println(err)
				break
			}
			fmt.Println("receive:", data)
		}
	}).Run()

	time.Sleep(time.Second)

	// Client
	conn, err := gtcp.NewConn("127.0.0.1:8999")
	if err != nil {
		panic(err)
	}
	defer conn.Close()
	for i := 0; i < 10000; i++ {
		if err := conn.SendPkg([]byte(gconv.String(i))); err != nil {
			glog.Error(err)
		}
		time.Sleep(1*time.Second)
	}
}
```
这个示例比较简单，执行后，输出结果为：
```html
receive: [48]
receive: [49]
receive: [50]
receive: [51]
...
```

## 使用示例2，自定义数据结构

大多数场景下，我们需要对发送的消息能自定义数据结构，开发者可以利用`数据`字段传递任意的消息内容实现。

以下是一个简单的自定义数据结构的示例，用于客户端上报当前主机节点的内存及CPU使用情况，示例代码位于：https://github.com/gogf/gf/tree/master/geg/net/gtcp/pkg_operations/monitor

在该示例中，`数据`字段使用了`JSON`数据格式进行自定义，便于数据的编码/解码。

1. `types/types.go`

    数据结构定义。
    ```go
    package types

    import "github.com/gogf/gf/g"

    type NodeInfo struct {
        Cpu       float32 // CPU百分比(%)
        Host      string  // 主机名称
        Ip        g.Map   // IP地址信息(可能多个)
        MemUsed   int     // 内存使用(byte)
        MemTotal  int     // 内存总量(byte)
        Time      int     // 上报时间(时间戳)
    }
    ```
1. `gtcp_monitor_server.go`

    服务端。
    ```go
    package main

    import (
        "encoding/json"
        "github.com/gogf/gf/g/net/gtcp"
        "github.com/gogf/gf/g/os/glog"
        "github.com/gogf/gf/geg/net/gtcp/pkg_operations/monitor/types"
    )

    func main() {
        // 服务端，接收客户端数据并格式化为指定数据结构，打印
        gtcp.NewServer("127.0.0.1:8999", func(conn *gtcp.Conn) {
            defer conn.Close()
            for {
                data, err := conn.RecvPkg()
                if err != nil {
                    if err.Error() == "EOF" {
                        glog.Println("client closed")
                    }
                    break
                }
                info := &types.NodeInfo{}
                if err := json.Unmarshal(data, info); err != nil {
                    glog.Errorfln("invalid package structure: %s", err.Error())
                } else {
                    glog.Println(info)
                    conn.SendPkg([]byte("ok"))
                }
            }
        }).Run()
    }
    ```
1. `gtcp_monitor_client.go`

    客户端。
    ```go
    package main

    import (
        "encoding/json"
        "github.com/gogf/gf/g"
        "github.com/gogf/gf/g/net/gtcp"
        "github.com/gogf/gf/g/os/glog"
        "github.com/gogf/gf/g/os/gtime"
        "github.com/gogf/gf/geg/net/gtcp/pkg_operations/monitor/types"
    )

    func main() {
        // 数据上报客户端
        conn, err := gtcp.NewConn("127.0.0.1:8999")
        if err != nil {
            panic(err)
        }
        defer conn.Close()
        // 使用JSON格式化数据字段
        info, err := json.Marshal(types.NodeInfo{
            Cpu       : float32(66.66),
            Host      : "localhost",
            Ip        : g.Map {
                "etho" : "192.168.1.100",
                "eth1" : "114.114.10.11",
            },
            MemUsed   : 15560320,
            MemTotal  : 16333788,
            Time      : int(gtime.Second()),
        })
        if err != nil {
            panic(err)
        }
        // 使用 SendRecvPkg 发送消息包并接受返回
        if result, err := conn.SendRecvPkg(info); err != nil {
            if err.Error() == "EOF" {
                glog.Println("server closed")
            }
        } else {
            glog.Println(string(result))
        }
    }
    ```
1. 执行后
    - 客户端输出结果为：
        ```html
        2019-05-03 13:33:25.710 ok
        ```
    - 服务端输出结果为：
        ```html
        2019-05-03 13:33:25.710 &{66.66 localhost map[eth1:114.114.10.11 etho:192.168.1.100] 15560320 16333788 1556861605}
        2019-05-03 13:33:25.710 client closed
        ```
