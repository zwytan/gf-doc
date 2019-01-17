# gregex

`gregex`提供了对正则表达式的支持，底层是对标准库`regexp`的封装，极大地简化了正则的使用，并采用了解析缓存设计，提高了执行效率。

使用方式：
```go
import "gitee.com/johng/gf/g/util/gregex"
```

方法列表： [godoc.org/github.com/gogf/gf/g/util/gregex](https://godoc.org/github.com/gogf/gf/g/util/gregex)
```go
func IsMatch(pattern string, src []byte) bool
func IsMatchString(pattern string, src string) bool
func MatchAllString(pattern string, src string) ([][]string, error)
func MatchString(pattern string, src string) ([]string, error)
func Quote(s string) string
func Replace(pattern string, replace, src []byte) ([]byte, error)
func ReplaceFunc(pattern string, src []byte, repl func(b []byte) []byte) ([]byte, error)
func ReplaceString(pattern, replace, src string) (string, error)
func ReplaceStringFunc(pattern string, src string, repl func(s string) string) (string, error)
func Validate(pattern string) error
```

## 示例1，基本使用

```go
package main

import (
    "fmt"
    "gitee.com/johng/gf/g/util/gregex"
)

func main() {
    match, _ := gregex.MatchString(`(\w+).+\-\-\s*(.+)`, `GF is best! -- John`)
    fmt.Printf(`%s says "%s" is the one he loves!`, match[2], match[1])
}
```
执行后，输出结果为：
```html
John says "GF" is the one he loves!
```