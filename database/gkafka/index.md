# gkafka

`gkafka`模块实现了对kafka消息队列系统的客户端功能封装，支持`分组消费`及`指定起始位置`等特性，并提供简便易用的API接口。

使用方式：
```go
import "gitee.com/johng/gf/g/database/gkafka"
```

方法列表：[godoc.org/github.com/johng-cn/gf/g/database/gkafka](https://godoc.org/github.com/johng-cn/gf/g/database/gkafka)
```go
type Client
    func NewClient(config *Config) *Client
    func (client *Client) AsyncSend(message *Message) error
    func (client *Client) Close()
    func (client *Client) MarkOffset(topic string, partition int, offset int, metadata ...string) error
    func (client *Client) Receive() (*Message, error)
    func (client *Client) SyncSend(message *Message) error
    func (client *Client) Topics() ([]string, error)
type Config
    func NewConfig() *Config
type Message
    func (msg *Message) MarkOffset()
```

# 使用示例

## 生产者

```go
package main

import (
    "gitee.com/johng/gf/g/database/gkafka"
    "fmt"
    "gitee.com/johng/gf/g/os/gtime"
    "time"
)

// 创建kafka生产客户端
func newKafkaClientProducer(topic string) *gkafka.Client {
    kafkaConfig               := gkafka.NewConfig()
    kafkaConfig.Servers        = "localhost:9092"
    kafkaConfig.AutoMarkOffset = false
    kafkaConfig.Topics         = topic
    return gkafka.NewClient(kafkaConfig)
}

func main () {
    client := newKafkaClientProducer("test")
    defer client.Close()
    for {
        if err := client.SyncSend(&gkafka.Message{Value: []byte(gtime.Now().String())}); err != nil {
            fmt.Println(err)
        }
        time.Sleep(time.Second)
    }
}
```

## 消费者

```go
package main

import (
    "fmt"
    "gitee.com/johng/gf/g/database/gkafka"
)

// 创建kafka消费客户端
func newKafkaClientConsumer(topic, group string) *gkafka.Client {
    kafkaConfig               := gkafka.NewConfig()
    kafkaConfig.Servers        = "localhost:9092"
    kafkaConfig.AutoMarkOffset = false
    kafkaConfig.Topics         = topic
    kafkaConfig.GroupId        = group
    return gkafka.NewClient(kafkaConfig)
}

func main () {
    group  := "test-group"
    topic  := "test"
    client := newKafkaClientConsumer(topic, group)
    defer client.Close()

    // 标记开始读取的offset位置
    client.MarkOffset(topic, 0, 6)
    for {
        if msg, err := client.Receive(); err != nil {
            fmt.Println(err)
            break
        } else {
            fmt.Println(msg.Partition, msg.Offset, string(msg.Value))
            msg.MarkOffset()
        }
    }
}

```