# ghash

常用经典哈希函数Go语言实现，提供`uint32`及`uint64`类型的哈希函数。

**使用方式**：
```go
import "github.com/gogf/gf/g/encoding/ghash"
```
**接口文档**：[godoc.org/github.com/gogf/gf/g/encoding/ghash](https://godoc.org/github.com/gogf/gf/g/encoding/ghash)
```go
func APHash(str []byte) uint32
func APHash64(str []byte) uint64
func BKDRHash(str []byte) uint32
func BKDRHash64(str []byte) uint64
func DJBHash(str []byte) uint32
func DJBHash64(str []byte) uint64
func ELFHash(str []byte) uint32
func ELFHash64(str []byte) uint64
func JSHash(str []byte) uint32
func JSHash64(str []byte) uint64
func PJWHash(str []byte) uint32
func PJWHash64(str []byte) uint64
func RSHash(str []byte) uint32
func RSHash64(str []byte) uint64
func SDBMHash(str []byte) uint32
func SDBMHash64(str []byte) uint64
```