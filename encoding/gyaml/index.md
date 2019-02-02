# gyaml

`YAML`数据格式编码解析。

使用方式：
```go
import "github.com/gogf/gf/g/encoding/gyaml"
```

接口文档：[godoc.org/github.com/gogf/gf/g/encoding/gyaml](https://godoc.org/github.com/gogf/gf/g/encoding/gyaml)
```go
func Decode(v []byte) (interface{}, error)
func DecodeTo(v []byte, result interface{}) error
func Encode(v interface{}) ([]byte, error)
func ToJson(v []byte) ([]byte, error)
```