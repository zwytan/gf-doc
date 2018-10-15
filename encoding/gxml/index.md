
# gxml

XML数据格式编码解析。

使用方式：
```go
import "gitee.com/johng/gf/g/encoding/gxml"
```

方法列表：
```go
func Decode(xmlbyte []byte) (map[string]interface{}, error)
func Encode(v map[string]interface{}, rootTag ...string) ([]byte, error)
func EncodeWithIndent(v map[string]interface{}, rootTag ...string) ([]byte, error)
func ToJson(xmlbyte []byte) ([]byte, error)
```