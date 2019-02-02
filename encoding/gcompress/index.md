# gcompress

二进制数据压缩/解压，支持Zlib/GZip算法。

使用方式：
```go
import "github.com/gogf/gf/g/encoding/gcompress"
```

接口文档：
```go
func Gzip(data []byte) []byte
func UnGzip(data []byte) []byte
func UnZlib(data []byte) []byte
func Zlib(data []byte) []byte
```