
# gcharset

字符编码转换。

使用`mahonia`实现的字符集转换方法，支持的字符集: 
`utf8`/
`UTF-16`/
`UTF-16LE`/
`macintosh`/
`big5`/
`gbk`/
`gb18030`。

使用方式：
```go
import "gitee.com/johng/gf/g/encoding/gcharset"
```
接口文档：[godoc.org/github.com/gogf/gf/g/encoding/gcharset](https://godoc.org/github.com/gogf/gf/g/encoding/gcharset)
```go
func Convert(dstCharset string, srcCharset string, src string) (dst string, err error)
func ToUTF8(charset string, src string) (dst string, err error)
func UTF8To(charset string, src string) (dst string, err error)
```