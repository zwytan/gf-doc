# gmd5
MD5算法。

使用方式：
```go
import "gitee.com/johng/gf/g/crypto/gmd5"
```

接口文档：

```go
func Encrypt(v interface{}) string
func EncryptFile(path string) string
func EncryptString(v string) string
```