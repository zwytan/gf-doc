
# gxml

XML数据格式编码解析。

使用方式：
```go
import "github.com/gogf/gf/g/encoding/gxml"
```

接口文档：[godoc.org/github.com/gogf/gf/g/encoding/gxml](https://godoc.org/github.com/gogf/gf/g/encoding/gxml)
```go
func Decode(xmlbyte []byte) (map[string]interface{}, error)
func Encode(v map[string]interface{}, rootTag ...string) ([]byte, error)
func EncodeWithIndent(v map[string]interface{}, rootTag ...string) ([]byte, error)
func ToJson(xmlbyte []byte) ([]byte, error)
```