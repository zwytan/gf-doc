# gdes

DES算法。

使用方式：
```go
import "gitee.com/johng/gf/g/crypto/gdes"
```

**关于gdes包中的补位说明：
gdes包中补位方式支持：PKCS5PADDING、NOPADDING两种方式，当使用NOPADDING方式时需要自定义补位方法。**

**关于gdes包中的密钥的说明，当使用三倍长的DES算法时，密钥为16字节时，key3等于key1**


接口文档：
```go

//单倍长ECB模式的DES加密方法
//key必须为8字节长
func DesECBEncrypt(key []byte, clearText []byte, padding int) ([]byte, error)

//单倍长ECB模式的DES解密方法
func DesECBDecrypt(key []byte, cipherText []byte, padding int) ([]byte, error) 

//三倍长ECB模式的DES加密方法
//key的长度必须是16或24字节长，当key为16字节双倍长的密钥时k3=k1
func TripleDesECBEncrypt(key []byte, clearText []byte, padding int) ( []byte, error)

//三倍长ECB模式的DES解密方法
func TripleDesECBDecrypt(key []byte, cipherText []byte, padding int) ([]byte,  error)

//单倍长CBC模式DES加密方法
func DesCBCEncrypt(key []byte, clearText []byte, iv []byte, padding int) ([]byte, error)

//单倍长CBC模式DES解密方法
func DesCBCDecrypt(key []byte, cipherText []byte, iv []byte, padding int) ([]byte, error)

//三倍长CBC模式DES加密方法
func TripleDesCBCEncrypt(key []byte, clearText []byte, iv []byte, padding int) ([]byte, error)

//三倍长CBC模式DES解密方法
func TripleDesCBCDecrypt(key []byte, cipherText []byte, iv []byte, padding int) ( []byte,  error)
```