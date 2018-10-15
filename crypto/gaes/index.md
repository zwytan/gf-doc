# gaes
AES算法。

使用方式：
```go
import "gitee.com/johng/gf/g/crypto/gaes"
```

方法列表：

```go
func Decrypt(cipherText []byte, key []byte, iv ...[]byte) ([]byte, error)
func Encrypt(plainText []byte, key []byte, iv ...[]byte) ([]byte, error)
func PKCS5Padding(src []byte, blockSize int) []byte
func PKCS5UnPadding(src []byte) []byte
```