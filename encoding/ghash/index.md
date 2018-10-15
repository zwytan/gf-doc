# ghash

常用经典哈希函数，提供uint32及uint64类型的哈希函数。

使用方式：
```go
import "gitee.com/johng/gf/g/encoding/ghash"
```
方法列表：
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